# Step 0000 — Project Planning

## Goal

Establish the documentation foundation before writing any product code. Define architecture, key decisions, and the build roadmap so that every subsequent milestone has clear context and rationale.

## Summary

Created the `docs/` directory structure with architecture overviews, decision records, the build roadmap, and this implementation step document. No application code was written.

## Why This Change Was Made

Starting with documentation before code forces architectural decisions to be made explicitly rather than implicitly. It also creates a review point before any code is committed, ensures the whole plan is visible before execution begins, and creates a reference that survives across sessions and team members.

## Files Created

```
docs/
├── README.md
├── roadmap.md
├── architecture/
│   ├── overview.md
│   └── design-system.md
├── decisions/
│   ├── 0001-monorepo-adonis-api-tanstack-start.md
│   └── 0002-shadcn-ui-component-foundation.md
└── implementation-steps/
    └── 0000-project-planning.md    ← this file
```

## Files Modified

None (fresh project).

## Key Decisions

### Monorepo with AdonisJS API + TanStack Start

See `docs/decisions/0001-monorepo-adonis-api-tanstack-start.md`.

Short version:
- Separate API (AdonisJS v7) + web app (TanStack Start)
- API is pure JSON; no views
- Web app uses a BFF pattern: server functions hold the API token in HttpOnly cookie
- Browser never sees the raw API token
- Future mobile app reuses the same API unchanged

### shadcn/ui for frontend components

See `docs/decisions/0002-shadcn-ui-component-foundation.md`.

Short version:
- Copy-paste model: components live in `src/components/ui/`, not a node_module
- Accessible primitives (Radix UI) with Tailwind styling
- Add components only when a milestone needs them

### AdonisJS Plus for auth/account features

- `@adonisplus/persona` handles signup, login, 2FA, recovery codes, social auth, password reset, email verification, session tracking
- API-only flavor (no Inertia/React from Plus)
- Reduces months of auth boilerplate to configuration and light adaptation

### Opaque access tokens, not JWT

- DB-backed tokens via `@adonisjs/auth` `DbAccessTokensProvider`
- Logout actually revokes the token
- Simpler than JWT refresh token rotation

## How To Run

No app exists yet. Nothing to run.

## How To Test Manually

Read through the docs:

```
docs/README.md                              — start here
docs/roadmap.md                             — full milestone plan
docs/architecture/overview.md               — system architecture
docs/architecture/design-system.md          — UI component approach
docs/decisions/0001-...                     — monorepo/framework decision
docs/decisions/0002-...                     — shadcn/ui decision
```

Check that the architecture makes sense before proceeding to Milestone 1.

## Known Gaps / Not Done Yet

- No application code
- Package versions not yet pinned (happens in Milestone 1–3)
- Color tokens described as direction, not finalized values (finalized in Milestone 3)
- `packages/shared/` described but not created (deferred to when it's actually needed)
- Email provider choice not documented (SMTP config happens in Milestone 6)
- Deployment target not chosen (deferred to Milestone 17)

## Rollback Notes

This step is docs only. Rolling back means deleting the `docs/` directory. No app state is affected.

## Next Suggested Step

**Milestone 1 — Monorepo Foundation**

Create the monorepo workspace with `pnpm-workspace.yaml`, root `package.json`, `.nvmrc` (Node 24), and both app directories as empty-but-valid shells. Neither app needs to fully run yet — just the workspace plumbing.
