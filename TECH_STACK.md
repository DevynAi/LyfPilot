# LyfPilot — Tech Stack

> Every technology choice and why it was made

---

## Runtime & Language

| Component | Choice | Why |
|-----------|--------|-----|
| **Runtime** | Node.js 20+ | LTS, native ESM, excellent async I/O for service mesh |
| **Language** | TypeScript 5.9 (strict) | Type safety across 16 services + 4 apps, shared contracts |
| **Package Manager** | pnpm 9.15 | Fast, deterministic, workspace-native, disk-efficient |
| **Monorepo** | Turborepo 2.5 | Incremental builds, remote caching, task orchestration |

---

## Backend (16 NestJS Microservices)

| Component | Choice | Why |
|-----------|--------|-----|
| **Framework** | NestJS 10.4 | Dependency injection, module system, decorator-driven, enterprise patterns |
| **ORM** | Prisma | Type-safe queries, migrations, multi-tenant RLS support |
| **Queue** | BullMQ 5.21 | Redis-backed job queues, retries, cron scheduling, rate limiting |
| **Validation** | Zod 3.25 | Runtime + compile-time schema validation, shared across all packages |
| **WebSocket** | Socket.IO 4.7 | Reliable bidirectional communication with fallback transports |

---

## Frontend Applications

| App | Framework | UI System | Purpose |
|-----|-----------|-----------|---------|
| **Web Dashboard** | Next.js 14 | Tamagui 1.130 | Primary user interface |
| **Admin Console** | Next.js 14 | Tamagui | Internal operations |
| **Mobile** | React Native 0.76 + Expo 52 | Tamagui | iOS/Android native |
| **AR Glasses** | Vite + Three.js + WebXR | Custom | Hands-free interaction |

### Shared Frontend Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| React | 18.3 | UI rendering |
| Tamagui | 1.130 | Cross-platform design system |
| React Query | 5.62 | Server state management |
| React Hook Form | 7.53 | Form handling |
| Socket.IO Client | 4.8 | Real-time updates |
| i18next | 24.2 | Internationalization |
| date-fns | 3.6 | Date manipulation |

---

## Database & Storage

| Component | Choice | Why |
|-----------|--------|-----|
| **Primary Store** | PostgreSQL 14+ | ACID, row-level security, mature ecosystem |
| **Vector Search** | pgvector extension | Semantic embeddings (1536-dim) without a separate vector DB |
| **Cache** | Redis 7+ | Session storage, rate limiting, distributed locks, pub/sub |
| **Object Storage** | Cloud-native (S3/GCS) | Attachments, exports, backups |

---

## Authentication & Security

| Component | Choice | Why |
|-----------|--------|-----|
| **Primary Auth** | Passwordless magic links | Better UX, no credential to steal |
| **MFA** | TOTP + WebAuthn (passkeys) | Hardware-backed second factor |
| **OAuth** | Google/Apple/Microsoft with PKCE | Standard, secure third-party auth |
| **Session** | Server-side with device binding | Revocable, auditable, no client-side JWT leaks |
| **Encryption (rest)** | AES-GCM | Authenticated encryption for secrets vault |
| **Encryption (transit)** | TLS 1.3 | Industry standard transport security |
| **Hashing** | Argon2id | Memory-hard, resists GPU attacks |
| **Signing** | EdDSA / ES256 | Modern, compact JWT signatures |
| **Headers** | Helmet | Automatic security header configuration |

---

## Observability

| Component | Choice | Why |
|-----------|--------|-----|
| **Logging** | Pino | Structured JSON, minimal overhead, correlation IDs |
| **Tracing** | OpenTelemetry (OTLP) | Vendor-neutral distributed tracing across all services |
| **Metrics** | Prometheus | Standard metrics collection, alerting |
| **Dashboards** | Grafana | Real-time visualization, alerting |
| **Health Checks** | NestJS Terminus | Kubernetes-compatible readiness/liveness probes |

---

## Testing

| Layer | Tool | Purpose |
|-------|------|---------|
| **Unit / Integration** | Vitest 3.2 | Fast, ESM-native test runner |
| **E2E** | Playwright 1.58 | Cross-browser end-to-end testing |
| **Contract** | Pact 5+ | Consumer-driven contract testing between services |
| **Linting** | ESLint 9.34 | Code quality enforcement |
| **Formatting** | Prettier 3.6 | Consistent code style |
| **Pre-commit** | simple-git-hooks | Automated quality gates before commit |

---

## Infrastructure

| Component | Choice | Why |
|-----------|--------|-----|
| **IaC** | Terraform | Multi-environment provisioning, reproducible infra |
| **Containers** | Docker | Consistent service packaging |
| **Registry** | AWS ECR | Container image storage |
| **Orchestration** | Kubernetes | Production service orchestration and scaling |

---

## Shared Libraries (@pos/ scope)

Internal packages ensure consistency across the monorepo:

| Package | Purpose |
|---------|---------|
| `@pos/contracts` | Zod schemas — single source of truth for API shapes |
| `@pos/types` | Pure TypeScript interfaces (User, Device, Policy, etc.) |
| `@pos/utils` | Crypto, retry logic, circuit breaker, HTTP helpers |
| `@pos/logging` | Pino logger with correlation ID propagation (AsyncLocalStorage) |
| `@pos/observability` | OpenTelemetry SDK initialization |
| `@pos/testing` | Test factories, mocks, testcontainer helpers |
| `@pos/theme` | Tamagui design tokens (shared across web + native) |

---

*© 2024-2026 DevStudio AI. All rights reserved.*
