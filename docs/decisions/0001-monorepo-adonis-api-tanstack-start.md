# ADR 0001 — Monorepo with AdonisJS API + TanStack Start

**Status:** Accepted

---

## Context

TrainAudit needs a full-stack architecture that:

1. Serves a web app today
2. Can serve a mobile app (React Native) in the future without rewriting the backend
3. Allows the backend and frontend to be developed and deployed independently
4. Uses TypeScript throughout for type safety and developer experience
5. Has a good auth story that works for both web (secure cookies) and mobile (token-based)

The decision is about: framework choice, repo structure, and how the web frontend talks to the API.

---

## Decision

**Use a pnpm monorepo** with two apps:

- `apps/api` — AdonisJS v7, API mode only (no views, no SSR, JSON only)
- `apps/web` — TanStack Start (React, SSR via Nitro, TanStack Router)

**AdonisJS for the API** because:
- First-class TypeScript; the framework is designed for it, not bolted on
- AdonisJS Plus provides production-ready auth, 2FA, social auth, password management, and session tracking — avoiding months of boilerplate
- Lucid ORM integrates cleanly; migrations, models, and relationships are straightforward
- API mode is minimal and focused; no template engine overhead
- Strong conventions reduce decision fatigue

**TanStack Start for the web app** because:
- Full SSR + file-based routing with TanStack Router
- TanStack Query for server state management (caching, mutations, loading states)
- Vite-based; fast dev experience
- React ecosystem access
- The BFF pattern fits naturally: server functions run on the Nitro server and can hold the API token securely

**Monorepo with pnpm workspaces** because:
- API and web can share TypeScript types from `packages/shared` when ready
- Single repo = single PR, single CI pipeline, unified tooling
- pnpm's workspace linking is efficient; no hoisting surprises

---

## BFF Auth Pattern

The web app uses a Backend-For-Frontend pattern for auth token storage.

```
Browser → TanStack Start server fn (holds token in HttpOnly cookie) → AdonisJS API (Bearer token)
```

Why:
- Browser JavaScript never sees the raw API token — XSS cannot steal it
- AdonisJS API stays pure Bearer-token-based — future mobile app uses it unchanged with Keychain/SecureStore
- No localStorage for sensitive tokens

The TanStack Start server uses a sealed session cookie (`SESSION_SECRET`) to store the opaque API token. The cookie is HttpOnly, Secure (in prod), and SameSite=Lax.

---

## Alternatives Considered

### Next.js instead of TanStack Start

- Larger ecosystem, more tutorials
- Rejected: TanStack Router's type-safe routing is superior; TanStack Query is the server state library anyway; Next.js App Router SSR model is more complex; TanStack Start is a better fit for a dedicated SPA-style app with a separate API

### NestJS instead of AdonisJS

- More popular in enterprise contexts
- Rejected: much more boilerplate; no equivalent of AdonisJS Plus; decorator-heavy style adds cognitive overhead; AdonisJS conventions are more ergonomic for this project size

### Monolith (one framework for both frontend and backend)

- e.g., AdonisJS + Inertia, or Remix, or Next.js API routes
- Rejected: couples frontend and backend deployments; AdonisJS Plus has an Inertia/React flavor but it locks us into a specific render model; a separate API is more flexible for mobile-first future expansion

### JWT instead of opaque tokens

- Simpler infrastructure (no DB lookup per request)
- Rejected: JWT cannot be revoked without a blocklist (which adds DB complexity anyway); opaque tokens with the `DbAccessTokensProvider` are simpler to reason about; logout actually works

### Turborepo for monorepo tooling

- More sophisticated build caching
- Deferred: adds complexity not needed at this stage; pnpm workspaces with simple scripts is sufficient for now; can be added later if build times become a problem

---

## Consequences

**Positive:**
- Clean API that mobile can reuse later with zero changes
- Auth is secure and follows the principle of least privilege (browser has no token)
- TypeScript end-to-end
- AdonisJS Plus handles the hardest auth/account features

**Negative / Trade-offs:**
- More moving parts than a monolith (two servers to run locally)
- TanStack Start server functions add a proxy hop for API calls (negligible latency in prod)
- BFF pattern requires careful handling of session cookie expiry vs. API token expiry
- TanStack Start is newer than Next.js; smaller community, fewer StackOverflow answers

---

## Related

- `docs/decisions/0002-shadcn-ui-component-foundation.md`
- `docs/architecture/overview.md`
