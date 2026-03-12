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
- Integration with Storage API, Encryption API, Manage API, Billing API

**State machine**:
```
CREATED → STARTING → RUNNING ↔ RESTARTING
                       ↓
                    STOPPING → STOPPED
                       ↓
                    DELETING → DELETED
```

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
- **Wake-up**: patches App CR `.spec.state = "Running"` directly (sandboxes service is not in the wake-up path)
- **Upstream URL**: resolved from `.status.appsProxy.upstreamUrl` in the App CR

This replaced the previous DNS-based approach where the proxy detected stopped apps via DNS resolution failures and routed wake-up requests through the Sandboxes Service API.

### Activity Tracking

- On successful connection → sends `PATCH /apps/{id} {"lastRequestTimestamp": "..."}` to Sandboxes Service
- Throttled to max 1 notification/30 seconds per app
- WebSocket connections send continuous notifications (prevents auto-suspend)

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
