# Ambient Code Platform

Kubernetes-native AI automation platform that orchestrates agentic sessions through containerized microservices. Built with Go (backend, operator), NextJS + Shadcn (frontend), Python (runner), and Kubernetes CRDs.

> Technical artifacts still use "vteam" for backward compatibility.

## Structure

- `components/backend/` - Go REST API (Gin), manages K8s Custom Resources with multi-tenant project isolation
- `components/frontend/` - NextJS web UI for session management and monitoring
- `components/operator/` - Go Kubernetes controller, watches CRDs and creates Jobs
- `components/runners/ambient-runner/` - Python runner executing Claude Code CLI in Job pods
- `components/ambient-cli/` - Go CLI (`acpctl`), manages agentic sessions from the command line
- `components/public-api/` - Stateless HTTP gateway, proxies to backend (no direct K8s access)
- `components/ambient-api-server/` - Go REST API microservice (rh-trex-ai framework), PostgreSQL-backed
- `components/ambient-sdk/` - Go + Python + TS client SDK for the platform's public REST API
- `components/ambient-control-plane/` - Session reconciler bridging PostgreSQL and Kubernetes
- `components/ambient-mcp/` - MCP server (Go + mcp-go) for tool integration
- `components/open-webui-llm/` - Open WebUI LLM integration
- `components/manifests/` - Kustomize-based deployment manifests and overlays
- `e2e/` - Cypress end-to-end tests
- `docs/` - Astro Starlight documentation site
- `specs/` - Desired state of the system ([sessions](specs/sessions/), [agents](specs/agents/), [control-plane](specs/control-plane/), [integrations](specs/integrations/), [standards](specs/standards/))
- `workflows/` - Agent-consumable procedures ([sessions](workflows/sessions/), [control-plane](workflows/control-plane/), [integrations](workflows/integrations/))
- `skills/` - [Agent Skills](https://agentskills.io) (`.claude/skills` symlinks here; domain symlinks in `specs/{domain}/.agents/skills`)

## Key Files

- CRD definitions: `components/manifests/base/crds/agenticsessions-crd.yaml`, `projectsettings-crd.yaml`
- Session lifecycle: `components/backend/handlers/sessions.go`, `components/operator/internal/handlers/sessions.go`
- Auth & RBAC middleware: `components/backend/handlers/middleware.go`
- K8s client init: `components/operator/internal/config/config.go`
- Runner entry point: `components/runners/ambient-runner/main.py`
- Route registration: `components/backend/routes.go`
- Frontend API layer: `components/frontend/src/services/api/`, `src/services/queries/`
- OpenAPI spec (v2): `components/ambient-api-server/openapi/openapi.yaml`

## Session Flow

```
User Creates Session → Backend Creates CR → Operator Spawns Job →
Pod Runs Claude CLI → Results Stored in CR → UI Displays Progress
```

## Architecture Transition

The platform is migrating from v1 (K8s-native backend) to v2 (REST/PostgreSQL API Server). Both stacks run in parallel. The v2 API Server uses the rh-trex-ai framework with OIDC, PostgreSQL, and OpenAPI-generated SDKs. See `components/architecture.md` for the full diagram.

## Critical Conventions

Cross-cutting rules that apply across ALL components. Component-specific conventions live in
[specs/standards/](specs/standards/) (see [BOOKMARKS.md](BOOKMARKS.md) > Component Standards).

- **User token auth required**: All user-facing API ops use `GetK8sClientsForRequest(c)`, never the backend service account
- **No tokens in logs/errors/responses**: Use `len(token)` for logging, generic messages to users
- **OwnerReferences on all child resources**: Jobs, Secrets, PVCs must have controller owner refs
- **No `panic()` in production**: Return explicit `fmt.Errorf` with context
- **No `any` types in frontend**: Use proper types, `unknown`, or generic constraints
- **Feature flags strongly recommended**: Gate new features behind Unleash flags
- **No new CRDs**: Use repo files or PostgreSQL for new persistent storage
- **Conventional commits**: Squashed on merge to `main`
- **Verify contracts and references**: Before building on an assumption, verify the contract. After moving anything, grep scripts, workflows, manifests, and configs
- **Full-stack awareness**: Auth/credential/API changes must update ALL consumers in the same PR
- **Separate configuration from code**: Externalize via env vars, ConfigMaps, manifests, or feature flags
- **Image references must match across the stack**: After changing an image name or tag, grep all overlays, workflows, and ConfigMaps
- **Reconcile, don't create-or-skip**: Use update-or-create patterns
- **Restricted SecurityContext on all containers**: `runAsNonRoot`, drop `ALL` capabilities, `readOnlyRootFilesystem`

Component-specific conventions:
- Backend: [conventions](specs/standards/backend/conventions.spec.md), [error handling](specs/standards/backend/error-handling.spec.md), [K8s client](specs/standards/backend/k8s-client.spec.md)
- Frontend: [conventions](specs/standards/frontend/conventions.spec.md), [React Query](specs/standards/frontend/react-query.spec.md)
- Operator: [conventions](specs/standards/control-plane/conventions.spec.md)
- Security: [security standards](specs/standards/security/security.spec.md)

## Agent Skills

Skills in `skills/` (symlinked from `.claude/skills`):

| Skill | Purpose |
|-------|---------|
| `align` | Alignment checking |
| `amber-review` | Amber automation review |
| `control-plane/ambient` | Control plane development |
| `control-plane/dev-cluster` | Dev cluster management |
| `devflow` | Development workflow |
| `discover` | Codebase discovery |
| `frontend/cypress-demo` | Cypress demo management |
| `frontend/development` | Frontend development |
| `integrations/grpc-dev` | gRPC development |
| `integrations/unleash-flag` | Feature flag setup |
| `memory` | Memory management |
| `pr-fixer` | PR fix automation |
| `scaffold` | Code scaffolding |
| `sessions/ambient-api-server` | API Server session management |
| `spec` | Spec management |

## Review Agents

Specialized review agents in `.claude/agents/`:
`backend-review.md`, `frontend-review.md`, `operator-review.md`, `runner-review.md`, `security-review.md`, `convention-eval.md`

## CI/CD Highlights

Key GitHub Actions in `.github/workflows/`:

| Workflow | Purpose |
|----------|---------|
| `lint.yml` | Pre-commit hooks on all files |
| `unit-tests.yml` | Go + frontend unit tests |
| `e2e.yml` | Cypress E2E with Kind |
| `components-build-deploy.yml` | Build + push images |
| `prod-release-deploy.yaml` | Production deployment |
| `sdk-version-bump.yml` | Daily SDK update checks |
| `sdd-preflight.yml` | Constitution compliance |

## PR Review Gate

A PreToolUse hook (`scripts/hooks/pr-review-gate.sh`) enforces lint, format, secrets scan, and CodeRabbit review before PR creation. Agents must also self-review against conventions before creating PRs.

## Testing

- **Backend**: `cd components/backend && make test`
- **Frontend**: `cd components/frontend && npx vitest run --coverage`
- **Runner**: `cd components/runners/ambient-runner && python -m pytest tests/`
- **E2E**: `cd e2e && npx cypress run --browser chrome`

## Detailed Documentation

For deep-dive analysis with architecture diagrams, data models, and workflow details, see `.agents/summary/index.md`.

## Convention Authority

This file and [BOOKMARKS.md](BOOKMARKS.md) are the authoritative source of project conventions. [CLAUDE.md](CLAUDE.md) contains the same conventions plus additional development standards. If they conflict, CLAUDE.md wins.

## Custom Instructions

<!-- This section is maintained by developers and agents during day-to-day work.
     It is NOT auto-generated by codebase-summary and MUST be preserved during refreshes.
     Add project-specific conventions, gotchas, and workflow requirements here. -->
