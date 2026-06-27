# TrainAudit — Architecture Overview

## Product Summary

TrainAudit is an AI-powered workout plan auditor, program compiler, workout tracker, and adaptive training block manager.

Core positioning: *"Turn any workout plan into a trackable, adaptive training program."*

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                          Client (Browser)                           │
│                                                                     │
│   TanStack Start (React + TanStack Router)                          │
│   Tailwind CSS + shadcn/ui                                          │
│   TanStack Query (server state)                                     │
└───────────────────────────┬─────────────────────────────────────────┘
                            │ HTTP (same origin, HttpOnly cookie)
┌───────────────────────────▼─────────────────────────────────────────┐
│                     TanStack Start Server (BFF)                     │
│                                                                     │
│   Server Functions / API Routes (Nitro)                             │
│   Reads API token from sealed HttpOnly cookie                       │
│   Forwards requests to AdonisJS API with Bearer token               │
│   Sets/clears cookie on login/logout                                │
└───────────────────────────┬─────────────────────────────────────────┘
                            │ HTTP (internal / localhost in dev)
                            │ Bearer token (opaque, DB-backed)
┌───────────────────────────▼─────────────────────────────────────────┐
│                       AdonisJS API (apps/api)                       │
│                                                                     │
│   AdonisJS v7, API mode only (no views, no Inertia)                 │
│   TypeScript + Lucid ORM                                            │
│   PostgreSQL                                                        │
│   AdonisJS Plus (persona, auth, social, 2FA, profile, tokens)       │
│   JSON responses only                                               │
│   OpenAI (real AI, deferred to Milestone 16)                        │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Monorepo Structure

```
trainaudit/
├── apps/
│   ├── api/          AdonisJS v7 API backend
│   └── web/          TanStack Start frontend
├── packages/
│   └── shared/       Shared TypeScript types (introduced later)
├── docs/             All documentation
├── pnpm-workspace.yaml
└── package.json      Root scripts only
```

Package manager: **pnpm** with workspaces.

Node version: **24** (pinned via `.nvmrc`). Required for AdonisJS v7's native TypeScript type-stripping (needs ≥22.18) and Vite 8/Rolldown (needs ≥20.19).

---

## Backend (apps/api)

**Framework:** AdonisJS v7, API mode

**Language:** TypeScript

**Database:** PostgreSQL via Lucid ORM

**Auth strategy:**
- Opaque access tokens (DB-backed via `@adonisjs/auth` `DbAccessTokensProvider`)
- Not JWT — opaque tokens allow server-side revocation, which JWT cannot do cleanly
- Tokens are never sent to the browser directly; the BFF layer holds them

**AdonisJS Plus:**
- `@adonisplus/persona` handles: signup, login, email verification, password reset, TOTP 2FA, recovery codes, social identity linking, personal tokens, session tracking
- API-only flavor used — no Inertia, no React from Plus
- See `docs/decisions/0001-monorepo-adonis-api-tanstack-start.md` for rationale

**Social auth:** AdonisJS Ally (via Plus) — Google OAuth. API runs the OAuth dance; callback returns a one-time code (hashed, single-use, 2-minute TTL) to the web BFF, which exchanges it for a real token server-to-server. No raw token in redirect URLs.

**2FA:** TOTP (Time-based OTP). Login creates a pending challenge record; full token only issued after OTP verification. Recovery codes as fallback.

**API contract:**
- All responses are JSON
- Routes prefixed `/api/v1/`
- Strict validation on all inputs
- Rate limiting on sensitive endpoints (auth, password reset)

---

## Frontend (apps/web)

**Framework:** TanStack Start (React + Nitro server)

**Routing:** TanStack Router (file-based)

**Server state:** TanStack Query

**Styling:** Tailwind CSS + shadcn/ui (see `docs/architecture/design-system.md`)

**Auth on the frontend:**
- Browser never sees the API token
- TanStack Start server functions hold the token in a sealed HttpOnly cookie (`useSession` / `SESSION_SECRET`)
- API calls go: browser → TanStack Start server fn → AdonisJS API (with Bearer token)
- This is the BFF (Backend-For-Frontend) pattern

**Route protection:**
- Protected routes: redirect to login if no valid session cookie
- Guest-only routes (login, register): redirect to dashboard if already logged in

---

## Auth Token Storage — Key Decision

| Concern | Choice | Reason |
|---|---|---|
| Token location | HttpOnly cookie (web) | Browser JS can't steal it; resistant to XSS |
| Token type | Opaque (DB-backed) | Revocable; simpler than JWT refresh flows |
| Cookie scope | SameSite=Lax, Secure | CSRF protection; HTTPS-only in prod |
| Mobile future | Bearer token from API | Mobile uses Keychain/SecureStore; same API works |

---

## Database

**Engine:** PostgreSQL 14+

**ORM:** Lucid (AdonisJS)

**Dev environment:** Docker Compose (Milestone 4)

**DB name:** `trainaudit` (prod) / `trainaudit_dev` (local)

---

## AI Layer

Introduced in Milestone 11 (mock) and Milestone 16 (real).

Architecture:
- `AiProvider` interface
- `MockAiProvider` for development/testing
- `OpenAiProvider` for production
- JSON schema validation on all AI responses
- Retry/repair loop for malformed JSON

---

## Environment Separation

| Environment | API URL | DB | AI |
|---|---|---|---|
| Local dev | `http://localhost:3333` | Docker PostgreSQL | Mock provider |
| Staging | TBD | Managed PostgreSQL | OpenAI (low limits) |
| Production | TBD | Managed PostgreSQL | OpenAI |

---

## Security Principles

- No secrets in code or git history
- `.env.example` files document required variables
- Rate limiting on auth endpoints
- Input validation via AdonisJS VineJS on all endpoints
- SQL injection protection via Lucid ORM (parameterized queries)
- XSS protection: tokens never in browser JS, React escapes by default
- CORS: configured to allow only the web app origin

---

## What This Architecture Defers

- Email delivery (Milestone 6+) — dev logs links, prod needs SMTP
- Real AI calls (Milestone 16)
- Permissions/RBAC (post-MVP)
- Multi-tenancy (not planned)
- CDN/file storage (not planned for MVP)
