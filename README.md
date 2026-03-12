<p align="center">
  <img src="docs/assets/lyfpilot-banner.png" alt="LyfPilot Banner" width="800" />
</p>

<h1 align="center">LyfPilot</h1>

<p align="center">
  <strong>Your AI-Powered Personal Operating System</strong>
</p>

<p align="center">
  A cloud-based personal OS that uses your own AI keys to automate your life — while keeping you firmly in control. Every AI suggestion must pass through a policy engine before anything happens, so nothing runs without your approval.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/platforms-Web%20%7C%20iOS%20%7C%20Android-blue" alt="Platforms" />
  <img src="https://img.shields.io/badge/integrations-12%2B-green" alt="Integrations" />
  <img src="https://img.shields.io/badge/AI-BYOK%20Multi--Provider-purple" alt="AI" />
  <img src="https://img.shields.io/badge/security-SOC2%20Ready-red" alt="Security" />
  <img src="https://img.shields.io/badge/status-Active%20Development-yellow" alt="Status" />
</p>

---

## How It Works

LyfPilot connects to the tools you already use — Gmail, Calendar, Slack, GitHub, Notion, and more — and lets AI help you manage everything from a single dashboard. The key difference from other AI assistants: **your AI never acts alone**.

```
You ask something → AI proposes a plan → Policy engine checks it → Only then does it execute
```

This means you get the speed of AI automation with the safety of human-approved guardrails. You bring your own API keys (OpenAI, Anthropic, Google, etc.), so there's no vendor lock-in and your data stays yours.

---

## Key Capabilities

### 🧠 Intelligent Automation
- **Natural language task execution** — Tell it what you want in plain English
- **Smart model routing** — Automatically picks the best AI model for each task (cost vs. speed vs. quality)
- **Long-term memory** — Remembers your preferences and context across conversations
- **Workflow templates** — Create reusable automations with custom triggers

### 🔐 Policy-Controlled Safety
- **Deterministic policy engine** — Every AI action filtered through rule-based approval
- **Capability-based access** — Fine-grained control over what AI can and can't do
- **Approval gates** — Sensitive actions require explicit confirmation
- **Full audit trail** — Every decision logged with immutable, append-only records

### 🔌 12+ Integrations
| | | |
|---|---|---|
| Google Workspace (Gmail, Calendar, Drive) | Microsoft 365 (Outlook, Teams, OneDrive) | Slack |
| GitHub | Linear | Notion |
| Todoist | Spotify | Jira |
| Trello | Dropbox | Zoom |
| Plaid (Banking) | Google Maps | OpenWeather |

Each integration uses OAuth2 with PKCE, encrypted token storage, and least-privilege scopes.

### 📱 Multi-Platform
- **Web Dashboard** — Full-featured Next.js interface
- **Mobile Apps** — Native iOS/Android via React Native + Expo
- **AR Glasses** — Experimental WebXR companion for hands-free interaction
- **Admin Console** — Internal operations dashboard

### 🔑 Bring Your Own Keys (BYOK)
Use your own API keys for any supported AI provider:
- OpenAI (GPT-4, GPT-4o)
- Anthropic (Claude)
- Google (Gemini)
- Azure OpenAI
- And more via extensible provider system

No middleman. No markup. Your keys, your models, your data.

---

## Architecture Overview

LyfPilot is built as a **service-oriented monorepo** — 16 independent NestJS microservices coordinated through a central API gateway.

```
┌─────────────────────────────────────────────────────┐
│                    Client Apps                       │
│         Web  ·  Mobile  ·  AR Glasses               │
└──────────────────────┬──────────────────────────────┘
                       │ HTTPS + WebSocket
                       ▼
┌─────────────────────────────────────────────────────┐
│              API Gateway (NestJS)                    │
│      Auth · Validation · Rate Limiting · Routing    │
└──────────────────────┬──────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
┌──────────────┐ ┌──────────┐ ┌──────────────┐
│ Orchestration│ │   LLM    │ │  Integrations│
│   Service    │ │  Service │ │   Service    │
│              │ │          │ │              │
│ Intent →     │ │ BYOK     │ │ 12+ Provider │
│ Plan →       │ │ Multi-   │ │ OAuth2/PKCE  │
│ Execute      │ │ Provider │ │ Token Vault  │
└──────┬───────┘ └────┬─────┘ └──────────────┘
       │              │
       ▼              ▼
┌──────────────┐ ┌──────────────┐
│   Policy     │ │   Executor   │
│   Engine     │ │   Service    │
│              │ │              │
│ Deterministic│ │ Sandboxed    │
│ Rule-Based   │ │ Idempotent   │
│ Decisions    │ │ Actions      │
└──────────────┘ └──────────────┘
       │
       ▼
┌─────────────────────────────────────────────────────┐
│                  Supporting Services                 │
│  Identity · Audit · Vault · Context Graph ·         │
│  Notifications · Scheduler · Billing · Analytics    │
└─────────────────────────────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────────────────────┐
│               Data Layer                            │
│    PostgreSQL + pgvector  ·  Redis  ·  Object Store │
└─────────────────────────────────────────────────────┘
```

> For the full architecture deep-dive, see [ARCHITECTURE.md](ARCHITECTURE.md)

---

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Runtime** | Node.js 20+, TypeScript 5.9 (strict ESM) |
| **Backend** | NestJS 10, Prisma ORM, BullMQ |
| **Frontend** | Next.js 14, React 18, Tamagui |
| **Mobile** | React Native 0.76, Expo 52 |
| **Database** | PostgreSQL 14+ with pgvector |
| **Cache** | Redis 7+ |
| **Auth** | Passwordless + OAuth + MFA/WebAuthn |
| **Observability** | OpenTelemetry, Prometheus, Grafana, Pino |
| **Testing** | Vitest, Playwright, Pact |
| **Monorepo** | Turborepo + pnpm workspaces |

> Full breakdown in [TECH_STACK.md](TECH_STACK.md)

---

## Security Model

Security isn't an afterthought — it's the architectural foundation.

| Layer | Protection |
|-------|-----------|
| **Network** | TLS 1.3, WAF, rate limiting (per-user/IP/tenant) |
| **Authentication** | Passwordless magic links + MFA (TOTP + WebAuthn passkeys) |
| **Authorization** | Capability-based access control, per-request policy checks |
| **AI Safety** | LLM proposes → Policy decides → Executor acts (never direct execution) |
| **Encryption** | AES-GCM at rest, TLS in transit, Argon2id hashing, EdDSA signing |
| **Secrets** | Dedicated vault service with key rotation |
| **Audit** | Append-only immutable event log with correlation IDs |
| **Tenant Isolation** | Hard partitioning at database, cache, and log levels |
| **Compliance** | GDPR (export/erasure/consent), SOC 2 readiness |

> Full security architecture in [SECURITY.md](SECURITY.md)

---

## Screenshots

<p align="center">
  <em>Screenshots coming soon — the product is in active development.</em>
</p>

<!-- 
<p align="center">
  <img src="docs/screenshots/dashboard.png" alt="Dashboard" width="600" />
  <br><em>Main Dashboard — Unified view of your connected services</em>
</p>

<p align="center">
  <img src="docs/screenshots/mobile.png" alt="Mobile App" width="300" />
  <br><em>Mobile App — Full functionality on the go</em>
</p>
-->

---

## Project Status

LyfPilot is in **active development**. Core infrastructure is built and functional:

- ✅ Service architecture (16 microservices)
- ✅ Authentication system (passwordless + MFA + OAuth)
- ✅ Policy engine with deterministic enforcement
- ✅ BYOK LLM integration with multi-provider routing
- ✅ 12+ third-party integrations with OAuth2
- ✅ Real-time WebSocket communication
- ✅ Multi-platform clients (web, mobile, AR)
- ✅ Full audit trail and observability stack
- 🔄 Public beta preparation

---

## License

This repository contains documentation and architecture references only. The source code is proprietary and not publicly available.

**© 2024-2026 DevynAi. All rights reserved.**

See [LICENSE](LICENSE) for details.
