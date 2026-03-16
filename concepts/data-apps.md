# Data Apps & Sandboxes - Architecture Overview

## System Overview

The system consists of several components that together manage the lifecycle of isolated computational environments (Jupyter notebooks, Streamlit apps, database workspaces, etc.):

```
                         ┌─────────────────────┐
                         │      User/Browser    │
                         └──────────┬───────────┘
                                    │
                          HTTPS (*.hub.keboola.com)
                                    │
                         ┌──────────▼───────────┐
                         │      Apps Proxy       │
                         │  (Go, reverse proxy)  │
                         │                       │
                         │ - Auth (OAuth/Basic)  │
                         │ - Wake-up sleeping    │
                         │ - Activity tracking   │
                         │ - WebSocket support   │
                         └──┬──────┬─────────┬───┘
                            │      │         │
     GET /apps/{id}/proxy-config   │   K8s API: watch/patch
     PATCH /apps/{id} (notify)     │   App CRs (state, wakeup)
                            │      │         │
                 ┌──────────▼──┐   │    ┌────▼────────────────┐
                 │  Sandboxes  │   │    │   Kubernetes API     │
                 │  Service    │   │    └────┬────────────────┘
                 └─────────────┘   │         │
                                   │    ┌────▼────────────────┐
                        ┌──────────▼─┐  │  Keboola Operator   │
                        │ App Pod    │  │  (Go, K8s operator)  │
                        │ (K8s)     │  │                      │
                        └────────────┘  │ - Reconciles CRDs   │
                              ▲         │ - Manages Deploys   │
                              │         │ - Token rotation    │
                     manages lifecycle  │ - Billing (AppRun)  │
                              │         └─────────────────────┘
                              │                  ▲
                 Sandboxes Service ───────────────┘
                   creates/patches App CRD
```

## Component 1: Sandboxes Service

**Repo**: `keboola/sandboxes-service`

**Role**: Central API and control plane for the entire system.

**Key responsibilities**:
- REST API for app CRUD (`POST/GET/PATCH/DELETE /apps`)
- Two provisioning strategies:
  - **JOB_QUEUE** (legacy): async jobs via Job Queue API, entity fields are source of truth, recovery mechanisms for stuck jobs
  - **OPERATOR** (new): creates/patches `App` CRDs in Kubernetes, CRD status is source of truth, no recovery needed
- App state machine: `AppState` (actual) + `DesiredAppState` (target)
- App type registry (Streamlit, PythonJs, Jupyter variants, DB workspaces) — each type generates its own CRD manifest
- Multi-cloud persistent storage (AWS EFS, Azure File Share, GCP Filestore)
- Proxy config endpoint consumed by Apps Proxy (`GET /apps/{id}/proxy-config`)
- Activity tracking (`lastRequestTimestamp`) for auto-suspend decisions (notified by Apps Proxy)
- **Auto-suspend CRON job**: periodically checks `lastRequestTimestamp` for each running app and stops idle apps by patching their App CRD state to `Stopped`
- Integration with Storage API, Encryption API, Manage API, Billing API

**State machine** (OPERATOR strategy — CRD status is source of truth):
```
CREATED → STARTING → RUNNING ↔ RESTARTING
                       │
                       ├──► STOPPING → STOPPED
                       │
                       └──► DELETING → DELETED
```

**Who drives each state transition**:

| Transition | Initiated by | Mechanism |
|---|---|---|
| CREATED → STARTING | Sandboxes Service | Creates App CRD with `spec.state=Running` |
| STARTING → RUNNING | Keboola Operator | Updates `status.currentState` when pod becomes ready |
| RUNNING → STOPPING (user) | Sandboxes Service | Patches App CRD `spec.state=Stopped` on user API call |
| RUNNING → STOPPING (auto-suspend) | Sandboxes Service | CRON job detects idle app, patches App CRD `spec.state=Stopped` |
| STOPPING → STOPPED | Keboola Operator | Scales Deployment to 0, updates `status.currentState` |
| STOPPED → STARTING (wake-up via URL) | Apps Proxy | User opens app URL; proxy patches `spec.state=Running` only (no deployment changes) |
| STOPPED → STARTING (wake-up via API) | Sandboxes Service | API call sets `desiredState=Running` only (no deployment changes) |
| STOPPED → STARTING (re-deploy via API) | Sandboxes Service | API call supplies `configVersion` + optionally `restartIfRunning: true`; regenerates App CRD with latest config |
| RUNNING → RESTARTING (re-deploy) | Sandboxes Service | API call supplies `configVersion` + `restartIfRunning: true`; regenerates App CRD with latest config |
| RUNNING → DELETING | Sandboxes Service | Deletes App CRD on user API call |
| DELETING → DELETED | Keboola Operator | Cleans up Deployment, Services, tokens; CRD is removed |

## Component 2: Keboola Operator

**Repo**: `keboola/keboola-operator`

**Role**: Kubernetes operator that reconciles `App` CRDs into actual running workloads.

### Custom Resources

- **App** (v1/v2) — desired and actual state of a data app
- **AppRun** — tracks individual Pod lifecycle for billing
- **StorageToken** — manages Keboola Storage API tokens as K8s resources
- **Workspace** — manages Keboola workspace provisioning

### Controllers (6 reconciliation loops)

| Controller | Watches | Does |
|---|---|---|
| **AppReconciler** | App CRD | Creates Deployments, Services, StorageTokens; manages start/stop |
| **StorageTokenReconciler** | StorageToken | Creates tokens via Manage API, stores in Secrets |
| **PodFailureReconciler** | Pods | Stops app on CrashLoopBackOff (when `autoRestartEnabled=false`) |
| **AppRunPodWatcherReconciler** | Pods | Creates/updates AppRun resources for billing |
| **AppRunReconciler** | AppRun | Marks AppRuns as synced (for billing export) |
| **StorageTokenCleanupReconciler** | ReplicaSets | Cleans up orphaned tokens after rolling updates |

### App CRD Features (optional, per-app)

- **StorageToken**: Auto-creates and mounts a scoped Storage API token
- **AppsProxyIngress**: Creates a Service for Apps Proxy traffic routing
- **DataDir**: EmptyDir + data-loader sidecar pulling from Storage API
- **MountConfig**: Fetches configs, transforms via JSONPath, mounts as Secrets

### State Mapping

- `spec.state: Running` → Deployment with replicas > 0
- `spec.state: Stopped` → Deployment with replicas = 0
- `status.currentState` reflects actual pod readiness

## Component 3: Apps Proxy

**Repo**: `keboola/keboola-as-code` (monorepo, `internal/pkg/service/appsproxy`)

**Role**: Reverse proxy that sits in front of all data apps, handling auth and wake-up.

### Request Flow

1. User hits `myapp-123abc.hub.keboola.com`
2. Proxy extracts AppID from subdomain
3. Loads app config from Sandboxes Service (cached with ETag)
4. Matches request path against auth rules
5. Enforces authentication (OAuth2 / Basic password / public)
6. Pre-checks app state from cached App CR status:
   - **Running** → proxy to upstream app pod
   - **Starting** → show loading spinner page
   - **Stopped/Stopping** → trigger wake-up, show loading spinner page
   - **AutoRestartEnabled=false** → show restart-disabled page

### Authentication

Providers: OIDC, GitHub, GitLab, JumpCloud, Basic password — configurable per-path with multi-provider selection page.

### Kubernetes Integration

Apps Proxy watches `App` CRs (`apps.keboola.com/v2`) directly via Kubernetes API:
- **State check**: reads `.status.currentState` from App CR to determine if the app is running, starting, or stopped
- **Wake-up**: patches App CR `.spec.state = "Running"` directly (sandboxes service is not in this wake-up path — see [Lifecycle Flows](#lifecycle-flows) for the full picture)
- **Upstream URL**: resolved from `.status.appsProxy.upstreamUrl` in the App CR

This replaced the previous DNS-based approach where the proxy detected stopped apps via DNS resolution failures and routed wake-up requests through the Sandboxes Service API.

### Activity Tracking

- On successful connection → sends `PATCH /apps/{id} {"lastRequestTimestamp": "..."}` to Sandboxes Service
- Throttled to max 1 notification/30 seconds per app
- WebSocket connections send continuous notifications (prevents auto-suspend)

## Lifecycle Flows

### Create

```
User API call
  → Sandboxes Service: validates request, persists app entity, creates App CRD (spec.state=Running)
    → Keboola Operator: reconciles CRD → creates Deployment, Service, StorageToken, etc.
      → Pod starts, becomes ready
    → Keboola Operator: updates CRD status.currentState=Running
  → Apps Proxy: K8s informer picks up the new App CR, app becomes routable
```

### Wake-up via app URL (user opens a stopped app)

When a user visits the URL of a stopped app, Apps Proxy handles the wake-up directly via the Kubernetes API — Sandboxes Service is not in this path. This is always a plain wake-up: the proxy only sets `spec.state=Running`, no deployment changes are made. The app starts with the previously deployed version as-is.

```
User opens app URL → Apps Proxy
  → Apps Proxy: reads App CR status → sees Stopped
  → Apps Proxy: patches App CR spec.state=Running (no other CRD fields changed)
  → Apps Proxy: renders loading spinner to user
    → Keboola Operator: reconciles → scales Deployment to replicas > 0 (existing Deployment spec unchanged)
      → Pod starts with previously deployed version, becomes ready
    → Keboola Operator: updates CRD status.currentState=Running
  → Apps Proxy: K8s informer sees Running → proxies next request to app pod
```

### Wake-up via UI/API (user clicks Start or calls API)

When a user starts an app from the Connection UI or via the Sandboxes Service API, the request goes through the standard API path. This supports two modes:

**Plain wake-up** — sets `desiredState=Running` only. No deployment changes; the app starts with the previously deployed version, same as wake-up via URL but routed through Sandboxes Service.

```
User clicks Start / API call with desiredState=Running
  → Sandboxes Service: patches App CRD spec.state=Running (no other CRD fields changed)
    → Keboola Operator: reconciles → scales Deployment to replicas > 0 (existing Deployment spec unchanged)
      → Pod starts with previously deployed version, becomes ready
    → Keboola Operator: updates CRD status.currentState=Running
  → Apps Proxy: K8s informer picks up Running state, app becomes routable
```

**Re-deploy** — supplies `configVersion` and optionally `restartIfRunning: true` (to re-deploy an already running app). Sandboxes Service regenerates the entire App CRD manifest from the specified config version (image, environment, mounts, etc.), so the Operator creates a new Deployment revision.

```
User clicks Deploy / API call with configVersion (+ restartIfRunning: true)
  → Sandboxes Service: regenerates App CRD manifest from the specified config version, applies it with spec.state=Running
    → Keboola Operator: reconciles → detects spec changes → performs rolling update of Deployment
      → New pod starts with updated config, becomes ready
    → Keboola Operator: updates CRD status.currentState=Running
  → Apps Proxy: K8s informer picks up Running state, app becomes routable
```

### Auto-suspend (idle app goes to sleep)

Auto-suspend takes the opposite path from wake-up: it flows through Sandboxes Service, not Apps Proxy. This asymmetry exists because suspend is not latency-sensitive and requires a centralized idle-time check across all apps.

```
Apps Proxy: on each proxied request, sends PATCH /apps/{id} with lastRequestTimestamp
  → Sandboxes Service: stores lastRequestTimestamp

Sandboxes Service CRON job (periodic):
  → checks lastRequestTimestamp for all running apps
  → for each idle app: patches App CRD spec.state=Stopped
    → Keboola Operator: reconciles → scales Deployment to 0
    → Keboola Operator: updates CRD status.currentState=Stopped
  → Apps Proxy: K8s informer sees Stopped → will trigger wake-up on next user request
```

### Delete

```
User API call
  → Sandboxes Service: marks app as deleting, deletes App CRD
    → Keboola Operator: reconciles deletion → removes Deployment, Service, Secrets, StorageTokens
    → Keboola Operator: CRD finalizer completes, CRD is removed
  → Sandboxes Service: marks app as deleted
```

## Component 4: Base Images

> TODO: Add documentation for base image repositories for data apps and sandboxes.

## Inter-Component Communication

```
Apps Proxy ──GET /apps/{id}/proxy-config──► Sandboxes Service
           ──PATCH /apps/{id} (notify)───►
           ──K8s Watch App CRs───────────► Kubernetes API (state monitoring)
           ──K8s Patch App CR (wakeup)───► Kubernetes API (sets spec.state=Running)
           ──HTTP proxy──────────────────► App Pod (upstream URL from App CR status)

Sandboxes Service ──App CRD create/patch──► Kubernetes API ──► Keboola Operator
                  ──App CRD status read───► Kubernetes API

Keboola Operator ──creates──► Deployment, Service, Secret, StorageToken
                 ──Manage API──► Storage API tokens
                 ──Storage API──► Config data, branch info
```
