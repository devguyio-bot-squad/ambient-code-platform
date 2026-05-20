# Ambient Code Platform — Documentation Index

> **For AI Assistants**: This file is your primary reference for navigating the codebase documentation. Read this file first, then consult specific documents as needed for detailed information.

## How to Use This Documentation

1. **Start here** — this index provides summaries and pointers to detailed files
2. **Consult specific files** based on your task:
   - Architecture questions → `architecture.md`
   - Component details → `components.md`
   - API/interface questions → `interfaces.md`
   - Data model questions → `data_models.md`
   - Build/deploy/CI questions → `workflows.md`
   - Dependency questions → `dependencies.md`
3. **Cross-reference** the codebase directly for implementation details

## Project Summary

The **Ambient Code Platform** is a Kubernetes-native AI automation platform that orchestrates intelligent agentic sessions through containerized microservices. Built with Go (backend, operator), Next.js + Shadcn (frontend), and Python (runner), it enables teams to create and manage AI-powered sessions via a web interface backed by Kubernetes Custom Resources.

**Key capabilities**: Multi-agent workflows, multi-git-provider support (GitHub, GitLab, Gerrit), real-time session monitoring, feature flags, and an architecture transitioning from K8s-native (v1) to REST/PostgreSQL (v2).

## Documentation Files

| File | Content | Consult When... |
|------|---------|-----------------|
| [codebase_info.md](codebase_info.md) | Project metadata, tech stack, language breakdown | You need a quick overview of the project, its tech stack, or supported languages |
| [architecture.md](architecture.md) | System architecture, design patterns, security model, v1→v2 transition | You need to understand how components connect, the session lifecycle, or the migration strategy |
| [components.md](components.md) | Detailed breakdown of all 12 components with key files and directories | You need to find specific code, understand a component's role, or locate entry points |
| [interfaces.md](interfaces.md) | REST APIs, CRD schemas, WebSocket/SSE, gRPC, SDK interfaces, MCP | You need API endpoints, CRD field definitions, or inter-component communication details |
| [data_models.md](data_models.md) | Core entities, backend types, CRD schemas, runner models, feature flags | You need data structures, type definitions, or entity relationships |
| [workflows.md](workflows.md) | Session lifecycle, dev workflow, CI/CD pipelines, PR review gate, testing | You need build/deploy commands, CI pipeline details, or development setup steps |
| [dependencies.md](dependencies.md) | Go/Python/JS dependencies, infrastructure services, external integrations | You need dependency versions, external service integration details, or CI tool info |

## Quick Reference

### Component Locations

| Component | Path | Language | Port |
|-----------|------|----------|------|
| Frontend | `components/frontend/` | TypeScript (Next.js) | 3000 |
| Backend API (v1) | `components/backend/` | Go (Gin) | 8080 |
| API Server (v2) | `components/ambient-api-server/` | Go (rh-trex-ai) | 8000 |
| Operator | `components/operator/` | Go (controller-runtime) | — |
| Control Plane | `components/ambient-control-plane/` | Go | 9080 |
| Runner | `components/runners/ambient-runner/` | Python (FastAPI) | — |
| CLI | `components/ambient-cli/` | Go | — |
| SDK | `components/ambient-sdk/` | Go + Python + TS | — |
| MCP Server | `components/ambient-mcp/` | Go | — |
| Public API | `components/public-api/` | Go | — |
| Manifests | `components/manifests/` | YAML (Kustomize) | — |

### Critical Files

| Purpose | File |
|---------|------|
| CRD definition | `components/manifests/base/crds/agenticsessions-crd.yaml` |
| Session handler (backend) | `components/backend/handlers/sessions.go` |
| Session handler (operator) | `components/operator/internal/handlers/sessions.go` |
| Auth middleware | `components/backend/handlers/middleware.go` |
| Route registration | `components/backend/routes.go` |
| Runner entry point | `components/runners/ambient-runner/main.py` |
| Frontend API layer | `components/frontend/src/services/api/` |
| React Query hooks | `components/frontend/src/services/queries/` |
| OpenAPI spec (v2) | `components/ambient-api-server/openapi/openapi.yaml` |

### Essential Commands

| Command | Purpose |
|---------|---------|
| `make kind-up` | Start local Kind cluster with full deployment |
| `make dev COMPONENT=frontend` | Hot-reload frontend against cluster |
| `make kind-rebuild` | Rebuild and redeploy all components |
| `make test-e2e` | Run Cypress E2E tests |
| `make lint` | Run all pre-commit linters |
| `make benchmark` | Run component benchmarks |

### Conventions (Critical)

- User token auth required for all API operations
- No `panic()` in Go production code — use `fmt.Errorf`
- No `any` types in frontend TypeScript
- OwnerReferences on all K8s child resources
- Feature flags gate new features (Unleash)
- Conventional commits, squash on merge
- No new CRDs — use PostgreSQL for new storage needs
