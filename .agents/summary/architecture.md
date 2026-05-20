# Architecture

## Overview

The Ambient Code Platform is a Kubernetes-native system that orchestrates AI agentic sessions. It follows a microservices architecture deployed on Kubernetes, with two parallel API stacks: a legacy K8s-native backend and a new REST/PostgreSQL-based API server.

## Architecture Diagram

```mermaid
graph TB
    subgraph "Clients"
        Browser["Browser"]
        CLI["acpctl CLI"]
        ExtSDK["External SDK Clients"]
    end

    subgraph "Frontend Layer"
        Frontend["Frontend\n(Next.js + Shadcn)\n:3000"]
    end

    subgraph "API Layer"
        Backend["Backend API (v1)\n(Go/Gin, K8s-native)\n:8080"]
        APIServer["API Server (v2)\n(rh-trex-ai)\n:8000"]
        PublicAPI["Public API Gateway\n(stateless proxy)"]
    end

    subgraph "Orchestration Layer"
        Operator["Operator\n(controller-runtime)"]
        ControlPlane["Control Plane\n(session reconciler)\n:9080"]
    end

    subgraph "Execution Layer"
        Runner["Ambient Runner\n(FastAPI + Claude CLI)"]
        StateSync["State Sync\n(S3 sidecar)"]
    end

    subgraph "Data Layer"
        K8s["Kubernetes API\n(CRDs)"]
        Postgres["PostgreSQL"]
        MinIO["MinIO\n(S3 storage)"]
    end

    subgraph "External Services"
        Claude["Anthropic Claude API"]
        GitHub["GitHub / GitLab / Gerrit"]
        Unleash["Unleash\n(Feature Flags)"]
    end

    Browser --> Frontend
    CLI --> APIServer
    ExtSDK --> APIServer

    Frontend --> Backend
    Frontend --> APIServer
    PublicAPI --> Backend

    Backend --> K8s
    APIServer --> Postgres
    ControlPlane --> Postgres
    ControlPlane --> K8s
    ControlPlane --> Runner

    Operator --> K8s
    Operator --> Runner

    Runner --> Claude
    Runner --> GitHub
    StateSync --> MinIO

    Backend --> Unleash
```

## Session Lifecycle

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant Backend
    participant K8s as Kubernetes API
    participant Operator
    participant Runner
    participant Claude as Claude API

    User->>Frontend: Create Agentic Session
    Frontend->>Backend: POST /api/sessions
    Backend->>K8s: Create AgenticSession CR
    K8s-->>Operator: Watch event (new CR)
    Operator->>K8s: Create Job + Secret + PVC
    K8s-->>Runner: Pod starts
    Runner->>Claude: Execute prompt via CLI
    Claude-->>Runner: AI response + tool use
    Runner->>K8s: Update CR status
    K8s-->>Backend: Watch event (status change)
    Backend-->>Frontend: SSE/WebSocket update
    Frontend-->>User: Display results
```

## Architecture Transition

The platform is transitioning from a v1 (K8s-native) to v2 (REST/PostgreSQL) architecture:

| Aspect | v1 (Legacy) | v2 (Target) |
|--------|-------------|-------------|
| Data Store | Kubernetes CRDs (etcd) | PostgreSQL |
| API Style | K8s-native via Backend | REST via API Server |
| Auth | User token → K8s RBAC | OIDC (built into rh-trex-ai) |
| Type Safety | Manual model sync | OpenAPI → Generated SDKs |
| Orchestration | Backend + Operator | Control Plane + Operator |

Both stacks run in parallel during migration. Frontend supports dual-mode toggling.

## Design Patterns

- **CRD-Driven**: Core entities (AgenticSession, ProjectSettings) are Kubernetes Custom Resources
- **Operator Pattern**: Controller watches CRDs, reconciles desired vs actual state
- **Sidecar Pattern**: State-sync container handles S3 persistence alongside runner
- **Proxy Pattern**: Frontend proxies API calls through Next.js API routes; Public API proxies to backend
- **Bridge Pattern**: Runner uses pluggable bridges (Claude, Gemini CLI, LangGraph) for AI provider abstraction
- **OpenAPI-First**: v2 stack generates SDKs from a canonical OpenAPI spec
- **Feature Flags**: Unleash gates new features with workspace-scoped overrides
- **AG-UI Protocol**: Runner exposes AG-UI compliant streaming endpoints for real-time event delivery

## Security Architecture

- User tokens required for all API operations (never service account for user-facing ops)
- OwnerReferences on all K8s child resources for automatic garbage collection
- Restricted SecurityContext: `runAsNonRoot`, drop `ALL` capabilities, `readOnlyRootFilesystem`
- No tokens in logs/errors/responses
- Per-session credential isolation
- NetworkPolicy for runner pods
