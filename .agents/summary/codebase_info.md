# Codebase Information

## Project

- **Name**: Ambient Code Platform
- **Repository**: `ambient-code/platform`
- **License**: MIT
- **Legacy name**: vTeam (technical artifacts still use "vteam" for backward compatibility)

## Description

Kubernetes-native AI automation platform that orchestrates intelligent agentic sessions through containerized microservices. Teams create and manage AI-powered sessions via a web interface, backed by Kubernetes Custom Resources and operators. Supports multi-agent workflows, multiple git providers (GitHub, GitLab, Gerrit), and real-time monitoring.

## Technology Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Backend API | Go + Gin | Go 1.24+ |
| Operator | Go + controller-runtime | Go 1.25 |
| Frontend | Next.js + Shadcn UI + React Query | Node.js 20+ |
| Runner | Python + FastAPI + Claude Code CLI | Python 3.11+ |
| API Server (v2) | Go + rh-trex-ai framework | Go 1.25 |
| Control Plane (v2) | Go | Go 1.25 |
| CLI | Go (`acpctl`) | Go 1.24 |
| SDK | Go + Python + TypeScript | Multi-language |
| MCP Server | Go + mcp-go | Go 1.24 |
| Database (v2) | PostgreSQL | — |
| Object Storage | MinIO (S3-compatible) | — |
| Feature Flags | Unleash | — |
| Observability | OpenTelemetry, Langfuse, Grafana | — |
| Deployment | Kubernetes (Kind, OpenShift), Kustomize | — |
| Container Engine | Podman or Docker | — |
| E2E Tests | Cypress | — |
| CI/CD | GitHub Actions | — |
| Code Review | CodeRabbit | — |
| Docs Site | Astro Starlight | — |

## Programming Languages

| Language | Usage | Components |
|----------|-------|------------|
| Go | Primary backend language | backend, operator, public-api, api-server, control-plane, CLI, MCP server, SDK (Go) |
| TypeScript/JavaScript | Frontend + SDK | frontend (Next.js), TypeScript SDK, E2E tests (Cypress) |
| Python | Runner + SDK + scripts | ambient-runner, Python SDK, utility scripts |
| YAML | Configuration | Kustomize manifests, GitHub Actions, CRD definitions |
| Shell (Bash) | Build/deploy scripts | Makefile targets, setup scripts, CI helpers |
| Mermaid | Diagrams | Architecture and flow diagrams |

## Key Metrics

- **Components**: 10+ containerized microservices
- **Go modules**: 9 independent modules
- **GitHub Actions workflows**: 24
- **CRDs**: 2 (AgenticSession, ProjectSettings)
- **Agent skills**: 15+
- **ADRs**: 9
- **Spec documents**: 22+
