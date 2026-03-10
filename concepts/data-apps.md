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
                         └──────┬───────┬────────┘
                                │       │
              ┌─────────────────┘       └──────────────────┐
              │ GET /apps/{id}/proxy-config                │
              │ PATCH /apps/{id} (wake-up, notify)         │
              │                                            │
   ┌──────────▼───────────┐                     ┌──────────▼──────────┐
   │  Sandboxes Service   │                     │   App Pod (K8s)     │
   │  (PHP/Symfony, API)  │                     │  (Streamlit, etc.)  │
   │                      │                     └─────────────────────┘
   │ - CRUD lifecycle API │                               ▲
   │ - State management   │                               │
   │ - Config validation  │                      manages lifecycle
   │ - Persistent storage │                               │
   └──────────┬───────────┘                     ┌─────────┴───────────┐
              │                                 │  Keboola Operator   │
              │ creates/patches App CRD         │  (Go, K8s operator) │
              │                                 │                     │
              └────────────────────────────────►│ - Reconciles CRDs   │
                                                │ - Manages Deploys   │
                    Kubernetes API               │ - Token rotation    │
                                                │ - Billing (AppRun)  │
                                                └─────────────────────┘
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
- Activity tracking (`lastRequestTimestamp`) for auto-suspend decisions
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
6. Proxies to upstream app pod via K8s service DNS

### Authentication

Providers: OIDC, GitHub, GitLab, JumpCloud, Basic password — configurable per-path with multi-provider selection page.

### Wake-up Mechanism

- DNS resolution failure → sends `PATCH /apps/{id} {"desiredState": "running"}` to Sandboxes Service
- Throttled to max 1 wake-up/second per app
- Shows loading spinner while app starts

### Activity Tracking

- On successful connection → sends `PATCH /apps/{id} {"lastRequestTimestamp": "..."}` to Sandboxes Service
- Throttled to max 1 notification/30 seconds per app
- WebSocket connections send continuous notifications (prevents auto-suspend)

## Component 4: Base Images

> TODO: Add documentation for base image repositories for data apps and sandboxes.

## Inter-Component Communication

```
Apps Proxy ──GET /apps/{id}/proxy-config──► Sandboxes Service
           ──PATCH /apps/{id} (wakeup)───►
           ──PATCH /apps/{id} (notify)───►
           ──DNS resolve──────────────────► K8s Service (to app pod)

Sandboxes Service ──App CRD create/patch──► Kubernetes API ──► Keboola Operator
                  ──App CRD status read───► Kubernetes API

Keboola Operator ──creates──► Deployment, Service, Secret, StorageToken
                 ──Manage API──► Storage API tokens
                 ──Storage API──► Config data, branch info
```
