# Apps Proxy Service

The Apps Proxy is a reverse proxy that sits in front of all [data apps](../concepts/data-apps.md), handling authentication, app wake-up, and request routing to containerized applications running in Kubernetes.

**Repo**: `keboola/keboola-as-code` (monorepo, `internal/pkg/service/appsproxy`)

## Primary Responsibilities

1. **Request Routing** — routes incoming requests to the correct data app based on the subdomain (e.g., `myapp-123abc.hub.keboola.com` maps to app `123abc`)
2. **Authentication & Authorization** — enforces per-path auth rules using configurable providers (OIDC, GitHub, GitLab, JumpCloud, Basic password)
3. **App Wake-Up** — detects stopped apps via Kubernetes API and triggers wake-up; shows a loading spinner while apps start. Sleep (auto-suspend) is not handled by the proxy — it is managed by a CRON job in [Sandboxes Service](../concepts/data-apps.md#component-1-sandboxes-service) that checks `lastRequestTimestamp`.
4. **Activity Tracking** — notifies Sandboxes Service of app usage (`lastRequestTimestamp`) so the Sandboxes Service CRON job can determine which apps to auto-suspend
5. **WebSocket Support** — proxies long-lived WebSocket connections with continuous keep-alive notifications

## Architecture

```
                    ┌──────────────────────┐
                    │    User / Browser     │
                    └──────────┬────────────┘
                               │
                     HTTPS (*.hub.keboola.com)
                               │
┌──────────────────────────────▼────────────────────────────────┐
│                        Apps Proxy                              │
│                                                                │
│  ┌────────────┐   ┌──────────────┐   ┌─────────────────────┐  │
│  │  AppConfig  │   │  App Router  │   │  Auth Handler       │  │
│  │ Middleware  │──▶│  (per-app    │──▶│  (OAuth2 / Basic /  │  │
│  │            │   │   handler)   │   │   public)           │  │
│  └────────────┘   └──────────────┘   └──────────┬──────────┘  │
│        │                                         │             │
│        │ config                         ┌────────▼──────────┐  │
│        │                                │ Upstream Handler   │  │
│        │                                │ (reverse proxy,    │  │
│        │                                │  wakeup, notify)   │  │
│        │                                └────────┬──────────┘  │
└────────┼─────────────────────────────────────────┼─────────────┘
         │                                         │
    ┌────▼─────────┐    ┌──────────────┐    ┌──────▼──────────┐
    │  Sandboxes   │    │  Kubernetes   │    │  App Pod (K8s)  │
    │  Service     │    │  API          │    │  (Streamlit,    │
    │              │    │              │    │   Jupyter, etc.) │
    └──────────────┘    └──────────────┘    └─────────────────┘
```

## Request Flow

1. User hits `myapp-123abc.hub.keboola.com`
2. **AppConfig Middleware** extracts AppID from the subdomain and loads app configuration from Sandboxes Service (cached with ETag)
3. **App Router** finds or creates a per-app handler; redirects if hostname doesn't match the canonical domain
4. **Auth Handler** matches the request path against auth rules (longest prefix wins) and enforces authentication if required
5. **Upstream Handler** checks app state from the Kubernetes App CR:
   - **Running** — proxies to the upstream app pod
   - **Starting** — renders a loading spinner page
   - **Stopped/Stopping** — patches App CR to trigger wake-up, renders spinner
6. On successful proxy, sends an activity notification to Sandboxes Service (throttled to 1/30s per app)

## Authentication

Auth rules are defined per-app in Sandboxes Service and matched by path prefix. Each rule specifies whether auth is required and which providers are allowed.

**Supported providers**: OIDC, GitHub OAuth, GitLab OAuth, JumpCloud, HTTP Basic password

**OAuth flow**: Uses [oauth2-proxy](https://github.com/oauth2-proxy/oauth2-proxy) under the hood. Session state is stored in per-app cookies (secret derived from app ID + provider ID + configurable salt). When multiple providers are configured for a path, a provider selection page is rendered.

**Headers injected on authenticated requests**: `X-Kbc-User-Name`, `X-Kbc-User-Email`, `X-Kbc-User-Roles`

## Integration Points

### Sandboxes Service

- `GET /apps/{id}/proxy-config` — loads app configuration (auth rules, providers, upstream settings). Cached in-process with ETag-based revalidation; falls back to stale cache (up to 1 hour) if Sandboxes Service is unreachable.
- `PATCH /apps/{id}` with `lastRequestTimestamp` — activity notification consumed by the Sandboxes Service CRON job for auto-suspend decisions

### Kubernetes API

Apps Proxy watches `App` CRs (`apps.keboola.com/v2`) directly via a Kubernetes informer:
- **State monitoring** — reads `.status.currentState` to determine if the app is running, starting, or stopped
- **Wake-up** — patches `.spec.state = "Running"` to trigger Keboola Operator reconciliation
- **Upstream URL** — resolved from `.status.appsProxy.upstreamUrl` in the App CR

This direct K8s integration replaced an earlier approach where the proxy detected stopped apps via DNS failures and routed wake-ups through the Sandboxes Service.

### OAuth Providers

Standard OAuth 2.0 / OIDC endpoints for external identity providers, configured per-app.

### Data App Pods

Upstream apps run in the same Kubernetes cluster. The proxy forwards HTTP and WebSocket traffic to the Service endpoint exposed by each app's Deployment (managed by Keboola Operator).

## Operational Aspects

### Caching

The service uses multiple cache layers to minimize external calls and tolerate transient failures:

| Cache | Source | Invalidation | Fallback |
|---|---|---|---|
| App config | Sandboxes Service | ETag / Cache-Control | Stale cache (1 hour) |
| App handler | Internal | Config or upstream URL change | Recreate on demand |
| App state | Kubernetes informer | Watch events (eventually consistent) | — |
| Notify throttle | Internal | 30-second window per app | — |
| Wakeup throttle | Internal | 1-second window per app | — |

### Timeouts

- HTTP request proxy: 30 seconds (configurable)
- WebSocket connections: 6 hours (configurable)

### Health & Observability

- Health check endpoint on the proxy port
- OpenTelemetry tracing and metrics
- Datadog integration (configurable)

## Related Concepts

- [Data Apps](../concepts/data-apps.md) — broader system overview including Sandboxes Service and Keboola Operator
- [Workspace](../concepts/workspace.md)

## Related APIs

- [Storage API](../apis/storage-api.md) — token validation for management API requests
