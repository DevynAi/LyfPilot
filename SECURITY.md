# LyfPilot — Security Model

> How LyfPilot protects your data, your AI, and your privacy

---

## Overview

LyfPilot's security architecture follows a **defense-in-depth** model with seven layers. The core principle: **AI never acts alone** — every AI-generated action must pass through a deterministic policy engine before execution.

---

## Seven-Layer Defense Model

```
┌─────────────────────────────────────────┐
│  Layer 7: Tenant Isolation              │
│  Hard partitioning at DB/cache/log      │
├─────────────────────────────────────────┤
│  Layer 6: Audit Trail                   │
│  Append-only, immutable event log       │
├─────────────────────────────────────────┤
│  Layer 5: Encryption                    │
│  AES-GCM at rest, TLS 1.3 in transit   │
├─────────────────────────────────────────┤
│  Layer 4: AI Safety Gate               │
│  Deterministic policy engine            │
├─────────────────────────────────────────┤
│  Layer 3: Authorization                 │
│  Capability-based access control        │
├─────────────────────────────────────────┤
│  Layer 2: Authentication                │
│  Passwordless + MFA + device binding    │
├─────────────────────────────────────────┤
│  Layer 1: Network                       │
│  TLS 1.3, WAF, rate limiting            │
└─────────────────────────────────────────┘
```

---

## Layer 1: Network Security

| Control | Implementation |
|---------|---------------|
| **Transport encryption** | TLS 1.3 for all connections |
| **Rate limiting** | Per-user, per-IP, and per-tenant thresholds |
| **DoS protection** | NestJS Throttler with adaptive rules |
| **Security headers** | Helmet middleware (CSP, HSTS, X-Frame-Options, etc.) |
| **CSRF protection** | Double-submit cookies + state binding |

---

## Layer 2: Authentication

### Passwordless (Primary)
- Email magic links with device binding
- Single-use, time-limited tokens
- No password to steal, phish, or brute-force

### Multi-Factor Authentication
- **TOTP** — Time-based one-time passwords (authenticator apps)
- **WebAuthn** — Hardware passkeys (YubiKey, Face ID, Windows Hello)
- Required for sensitive operations

### OAuth Login
- Google, Apple, Microsoft providers
- **PKCE** (Proof Key for Code Exchange) for all flows
- Strict redirect URI allowlist
- State parameter bound to tenant/user/nonce

### Session Management
- Server-side session records (not client-side JWT)
- Device binding (platform, app version, IP)
- Automatic token rotation
- Remote session revocation

---

## Layer 3: Authorization

### Capability-Based Access Control (CBAC)

Rather than simple role-based access, LyfPilot uses **capability-based authorization**:

```
User → UserDelegationRule → Capability Check → Allow/Deny
```

Each delegation rule specifies:
- **Capability** — What action is permitted
- **Constraints** — Max amount, time-of-day restrictions, rate limits
- **Scope** — Which integrations/resources are accessible
- **Enabled** — Rules can be toggled without deletion

This means every request — whether from a human or AI agent — is checked against fine-grained rules before execution.

---

## Layer 4: AI Safety Gate

The most distinctive security feature. **No LLM output ever directly triggers an action.**

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────┐
│   LLM        │────→│  Policy Engine   │────→│   Executor   │
│   Service    │     │  (Deterministic) │     │   Service    │
│              │     │                  │     │              │
│  Proposes    │     │  APPROVE         │     │  Acts        │
│  an action   │     │  DENY            │     │  (sandboxed) │
│              │     │  ESCALATE        │     │              │
└──────────────┘     └──────────────────┘     └──────────────┘
```

**Key properties:**
- Policy engine uses **deterministic rules** (not AI) — same input always gives same decision
- Sensitive actions trigger **escalation** — user approval required
- Actions execute in **sandboxed context** with blast radius containment
- Every decision is logged to the audit trail

---

## Layer 5: Encryption

| Scope | Method | Details |
|-------|--------|---------|
| **At rest** | AES-GCM | All secrets, tokens, and sensitive data in vault |
| **In transit** | TLS 1.3 | All HTTP and WebSocket connections |
| **Password/token hashing** | Argon2id | Memory-hard, resists GPU/ASIC attacks |
| **JWT signing** | EdDSA / ES256 | Modern elliptic curve signatures |
| **Key rotation** | Automated | Vault service handles periodic rotation |

### Secrets Vault
Integration tokens (OAuth refresh tokens, API keys) are encrypted before storage:
1. Token encrypted with AES-GCM using a derived key
2. Stored in dedicated vault table
3. Decrypted only at point of use
4. Rotation tracked and enforced

---

## Layer 6: Audit Trail

Every action in the system produces an immutable audit event:

| Field | Purpose |
|-------|---------|
| **Correlation ID** | Links all events in a single request chain |
| **Actor** | Who initiated (user, agent, system) |
| **Action** | What was attempted |
| **Policy Decision** | APPROVE / DENY / ESCALATE |
| **Result** | Success / failure / error |
| **Timestamp** | When it occurred |
| **Metadata** | Service, tenant, device context |

**Properties:**
- **Append-only** — Events are never modified or deleted
- **Non-repudiation** — Every action traceable to an actor
- **Correlation** — Full request chain reconstructable
- **Compliance-ready** — Supports GDPR auditing requirements

---

## Layer 7: Tenant Isolation

LyfPilot enforces hard boundaries between tenants at every level:

| Resource | Isolation Method |
|----------|-----------------|
| **Database** | Row-Level Security (RLS) via Prisma |
| **Cache** | Tenant-namespaced Redis keys |
| **Audit logs** | Tenant-scoped, no cross-tenant queries |
| **API keys** | Tenant-specific vault entries |
| **Rate limits** | Per-tenant and per-user thresholds |

---

## GDPR Compliance

| Right | Implementation |
|-------|---------------|
| **Right to access** | Data export API — full user data package |
| **Right to erasure** | Cascading deletion across all services |
| **Right to portability** | Standard JSON export format |
| **Consent management** | Granular consent tracking per integration |
| **Data minimization** | Only collect what's needed for functionality |

---

## Integration Security

All 12+ third-party integrations follow the same security pattern:

1. **OAuth2 with PKCE** — No implicit flow, no client secrets in browser
2. **Least-privilege scopes** — Request only the permissions needed
3. **Encrypted token storage** — Tokens encrypted in vault, never in plaintext
4. **Webhook signature verification** — HMAC validation on incoming webhooks
5. **Provider-specific error mapping** — Graceful handling of revoked access

---

*© 2024-2026 DevStudio AI. All rights reserved.*
