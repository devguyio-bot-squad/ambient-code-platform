# Dependencies

## Go Dependencies (Major)

### Backend (`ambient-code-backend`)
| Dependency | Purpose |
|-----------|---------|
| `github.com/gin-gonic/gin` | HTTP framework |
| `k8s.io/client-go` | Kubernetes client |
| `k8s.io/apimachinery` | K8s types and utilities |
| `github.com/gorilla/websocket` | WebSocket support (AG-UI proxy) |
| `github.com/Unleash/unleash-client-go` | Feature flag client |

### Operator (`ambient-code-operator`)
| Dependency | Purpose |
|-----------|---------|
| `sigs.k8s.io/controller-runtime` | Kubernetes controller framework |
| `k8s.io/client-go` | Kubernetes client |
| `k8s.io/apimachinery` | K8s types |

### API Server (`ambient-api-server`)
| Dependency | Purpose |
|-----------|---------|
| rh-trex-ai framework | REST API foundation |
| PostgreSQL driver | Database access |

### CLI (`ambient-cli/acpctl`)
| Dependency | Purpose |
|-----------|---------|
| Go SDK (`ambient-sdk/go-sdk`) | API client |
| CLI framework | Command-line interface |

### MCP Server (`ambient-mcp`)
| Dependency | Purpose |
|-----------|---------|
| `github.com/mark3labs/mcp-go` | MCP protocol implementation |

## Python Dependencies (Runner)

| Dependency | Purpose |
|-----------|---------|
| FastAPI | HTTP framework for runner endpoints |
| claude-agent-sdk | Claude Code CLI integration |
| AG-UI protocol | Agent UI event streaming |
| Langfuse | LLM observability tracing |
| MLflow | ML experiment tracking |
| gRPC | Status update transport |
| pytest | Test framework |
| ruff | Linting and formatting |

## JavaScript/TypeScript Dependencies

### Frontend (`ambient-runner-frontend`)
| Dependency | Purpose |
|-----------|---------|
| Next.js 15 | React framework |
| React | UI library |
| Shadcn UI | Component library |
| @tanstack/react-query | Data fetching and caching |
| TypeScript | Type safety |
| Tailwind CSS | Utility-first CSS |
| Vitest | Unit testing |

### TypeScript SDK (`@ambient-platform/sdk`)
| Dependency | Purpose |
|-----------|---------|
| Generated from OpenAPI | Type-safe API client |

### E2E Tests
| Dependency | Purpose |
|-----------|---------|
| Cypress | E2E testing framework |
| TypeScript | Test scripts |

## Infrastructure Dependencies

| Service | Purpose | Deployment |
|---------|---------|------------|
| Kubernetes | Container orchestration | Kind (local), OpenShift (prod) |
| PostgreSQL | v2 data storage | Kustomize manifest |
| MinIO | S3-compatible object storage | Kustomize manifest |
| Unleash | Feature flag management | Kustomize manifest |
| NGINX Ingress | HTTP routing (Kind) | Kind setup script |

## CI/CD Tool Dependencies

| Tool | Purpose |
|------|---------|
| GitHub Actions | CI/CD automation |
| pre-commit | Git hook framework |
| golangci-lint | Go linting |
| ESLint | JS/TS linting |
| ruff | Python linting/formatting |
| Vale | Documentation prose linting |
| markdownlint | Markdown formatting |
| cspell | Spell checking |
| shellcheck | Shell script linting |
| CodeRabbit | AI code review |
| Kustomize | K8s manifest management |
| Kind | Local Kubernetes clusters |
| Podman/Docker | Container builds |

## External Service Integrations

| Service | Purpose | Auth Method |
|---------|---------|-------------|
| Anthropic Claude API | AI model provider | API key |
| GitHub | Git provider, app auth | OAuth/GitHub App |
| GitLab | Git provider (SaaS + self-hosted) | OAuth |
| Gerrit | Git provider | — |
| Jira | Issue tracking | OAuth |
| LDAP | Directory services | Bind credentials |
| Google Vertex AI | Alternative AI provider | Application Default Credentials |
