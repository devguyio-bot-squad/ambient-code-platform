# Interfaces and APIs

## REST APIs

### Backend API (v1) — `:8080`

The legacy Go/Gin API, K8s-native. All endpoints require user token auth.

| Endpoint Group | Path Pattern | Handler File |
|---------------|-------------|--------------|
| Sessions | `/api/sessions/*` | `handlers/sessions.go` |
| Projects | `/api/projects/*` | `handlers/projects.go` |
| Repositories | `/api/repos/*` | `handlers/repository.go` |
| Secrets | `/api/secrets/*` | `handlers/secrets.go` |
| Permissions | `/api/permissions/*` | `handlers/permissions.go` |
| Runner Types | `/api/runner-types/*` | `handlers/runner_types.go` |
| Health | `/health` | `handlers/health.go` |
| Version | `/version` | `handlers/version.go` |
| GitHub Auth | `/api/github/*` | `handlers/github_auth.go` |
| GitLab Auth | `/api/gitlab/*` | `handlers/gitlab_auth.go` |
| Gerrit Auth | `/api/gerrit/*` | `handlers/gerrit_auth.go` |
| Jira Auth | `/api/jira/*` | `handlers/jira_auth.go` |
| LDAP | `/api/ldap/*` | `handlers/ldap.go` |
| OAuth | `/api/oauth/*` | `handlers/oauth.go` |
| Feature Flags | `/api/feature-flags/*` | `handlers/featureflags.go` |
| Feature Flags Admin | `/api/feature-flags/admin/*` | `handlers/featureflags_admin.go` |
| File Uploads | `/api/uploads/*` | `handlers/file_uploads.go` |
| Models | `/api/models/*` | `handlers/models.go` |
| Scheduled Sessions | `/api/scheduled-sessions/*` | `handlers/scheduled_sessions.go` |
| CodeRabbit Auth | `/api/coderabbit/*` | `handlers/coderabbit_auth.go` |
| MCP Credentials | `/api/mcp-credentials/*` | `handlers/mcp_credentials.go` |
| Display Name | `/api/display-name/*` | `handlers/display_name.go` |

Route registration: `routes.go`

### API Server (v2) — `:8000`

The new rh-trex-ai REST API. PostgreSQL-backed, OIDC built-in.

- OpenAPI spec: `components/ambient-api-server/openapi/openapi.yaml`
- Plugin-based: each kind (resource type) is a plugin under `plugins/`
- CRUD operations backed by PostgreSQL

### Public API Gateway

Stateless proxy that forwards requests to the backend with auth headers. No direct K8s access.

- Handlers: `components/public-api/handlers/`
- Types: `components/public-api/types/`

### Runner API — FastAPI

The ambient runner exposes HTTP endpoints for session management:

| Endpoint | File | Purpose |
|----------|------|---------|
| `/run` | `endpoints/run.py` | Start a session run |
| `/events` | `endpoints/events.py` | SSE event stream (AG-UI) |
| `/health` | `endpoints/health.py` | Health check |
| `/capabilities` | `endpoints/capabilities.py` | Runner capabilities |
| `/content` | `endpoints/content.py` | Content retrieval |
| `/feedback` | `endpoints/feedback.py` | User feedback |
| `/interrupt` | `endpoints/interrupt.py` | Interrupt a run |
| `/model` | `endpoints/model.py` | Model information |
| `/repos` | `endpoints/repos.py` | Repository info |
| `/tasks` | `endpoints/tasks.py` | Task management |
| `/workflow` | `endpoints/workflow.py` | Workflow management |
| `/mcp-status` | `endpoints/mcp_status.py` | MCP server status |

## Kubernetes CRDs

### AgenticSession (`agenticsessions.vteam.ambient-code/v1alpha1`)

Primary CRD for AI session management.

**Spec fields**:
- `repos[]` — Git repositories (url, branch, autoPush)
- `initialPrompt` — Session prompt
- `displayName` — Human-readable name
- `userContext` — Authenticated caller identity (userId, displayName, groups)
- `llmSettings` — Model, temperature, maxTokens
- `timeout` / `inactivityTimeout` — Session timeouts
- Additional fields for scheduling, SDK options, agents, etc.

**Status subresource**: Tracks session state and results.

CRD file: `components/manifests/base/crds/agenticsessions-crd.yaml`

### ProjectSettings (`projectsettings.vteam.ambient-code`)

CRD for per-project configuration.

CRD file: `components/manifests/base/crds/projectsettings-crd.yaml`

## Frontend API Routes (Next.js Proxy)

The frontend proxies backend calls through Next.js API routes:

| Route | Purpose |
|-------|---------|
| `src/app/api/ambient/` | Ambient API proxy |
| `src/app/api/auth/` | Authentication |
| `src/app/api/cluster-info/` | Cluster information |
| `src/app/api/config/` | Configuration |
| `src/app/api/feature-flags/` | Feature flag queries |
| `src/app/api/ldap/` | LDAP integration |
| `src/app/api/me/` | Current user info |
| `src/app/api/projects/` | Project management |
| `src/app/api/runner-types/` | Runner type listing |
| `src/app/api/version/` | Version info |
| `src/app/api/workflows/` | Workflow management |

## WebSocket / SSE

- **AG-UI Protocol**: Runner streams events via SSE; backend proxies via WebSocket
- **WebSocket proxy**: `components/backend/websocket/agui_proxy.go`
- **Event store**: `components/backend/websocket/agui_store.go`

## gRPC

- Runner uses gRPC transport for status updates: `ambient_runner/bridges/claude/grpc_transport.py`
- Control plane communicates via gRPC for session state sync

## SDK Interfaces

All SDKs are generated from the same OpenAPI spec:

| SDK | Path | Package |
|-----|------|---------|
| Go | `components/ambient-sdk/go-sdk/` | `github.com/ambient-code/platform/components/ambient-sdk/go-sdk` |
| Python | `components/ambient-sdk/python-sdk/` | — |
| TypeScript | `components/ambient-sdk/ts-sdk/` | `@ambient-platform/sdk` |

## MCP (Model Context Protocol)

- Server: `components/ambient-mcp/`
- Tools: `components/ambient-mcp/tools/`
- Token exchange: `components/ambient-mcp/tokenexchange/`
- Mention handling: `components/ambient-mcp/mention/`
