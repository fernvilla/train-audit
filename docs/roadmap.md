# TrainAudit Build Roadmap

Each milestone is a reviewable, committable unit. No milestone begins until the previous one is reviewed and committed.

---

## Milestone 0 — Project Planning Docs ✅

**Goal:** Define architecture, decisions, and roadmap before writing any code.

Deliverables:
- `docs/` structure
- Architecture overview
- Design system approach
- Monorepo decision record
- shadcn/ui decision record
- This roadmap

No product code.

---

## Milestone 1 — Monorepo Foundation

**Goal:** Establish the workspace. Both apps exist as runnable shells.

Deliverables:
- `pnpm-workspace.yaml`
- `apps/api/` placeholder (bare AdonisJS API app)
- `apps/web/` placeholder (bare TanStack Start app)
- Root `package.json` with workspace scripts
- Root `README.md`
- `.nvmrc` pinned to Node 24
- `docs/implementation-steps/0001-monorepo-foundation.md`

---

## Milestone 2 — AdonisJS API App

**Goal:** `apps/api` is a working AdonisJS v7 API app with a health check endpoint.

Deliverables:
- AdonisJS configured in API mode
- TypeScript configured
- Health check endpoint (`GET /health`)
- `.env.example`
- API runs locally on port 3333
- `docs/implementation-steps/0002-adonis-api-app.md`

---

## Milestone 3 — TanStack Start Web App + shadcn/ui Foundation

**Goal:** `apps/web` is a running TanStack Start app with Tailwind and the first shadcn/ui components.

Deliverables:
- TanStack Start with TanStack Router + React + TypeScript
- Tailwind CSS configured
- shadcn/ui configured (`components.json`)
- First components: `button`, `input`, `label`, `card`, `form`, `alert`, `separator`, `badge`, `skeleton`
- `apps/web/src/components/ui/`
- `apps/web/src/components/layouts/`
- `apps/web/src/lib/utils.ts`
- Basic landing page shell
- `docs/architecture/design-system.md` updated
- `docs/implementation-steps/0003-web-app-shadcn-foundation.md`

---

## Milestone 4 — Local Development Setup

**Goal:** Both apps run together with a real PostgreSQL database in Docker.

Deliverables:
- `docker-compose.yml` (PostgreSQL)
- `.env.example` files for both apps
- Local setup guide
- Dev scripts (single command to start everything)
- `docs/implementation-steps/0004-local-dev-setup.md`

---

## Milestone 5 — AdonisJS Plus Review

**Goal:** Install AdonisJS Plus (API-only flavor). Understand what it gives us before customizing.

Deliverables:
- Plus registry configured (`.npmrc`)
- `@adonisplus/persona` installed and scaffolded
- Inventory of what Plus generated
- Document what needs adaptation for pure API mode + BFF
- `docs/implementation-steps/0005-adonis-plus-review.md`

---

## Milestone 6 — Auth Foundation

**Goal:** Working register, login, logout, and current-user flow end-to-end.

Deliverables:
- Register endpoint
- Login endpoint (opaque access token issued)
- Logout endpoint (token revoked)
- `GET /auth/me` endpoint
- BFF cookie layer in TanStack Start (token stored in HttpOnly cookie, never exposed to browser)
- Frontend: register page, login page, protected dashboard shell, guest-only routes
- shadcn/ui form components used for auth forms
- `docs/implementation-steps/0006-auth-foundation.md`

---

## Milestone 7 — Account Foundation

**Goal:** Logged-in user can manage their profile and password.

Deliverables:
- Profile page (view + edit display name, avatar)
- Account settings page
- Password change
- shadcn/ui card/form components for account UI
- `docs/implementation-steps/0007-account-foundation.md`

---

## Milestone 8 — Social Auth (Google)

**Goal:** Users can sign in or register via Google OAuth.

Deliverables:
- AdonisJS Ally / Plus social auth wired to Google
- One-time code handoff pattern (API → web BFF, no token in redirect URL)
- Social account creation and linking
- `docs/implementation-steps/0008-social-auth.md`

---

## Milestone 9 — Two-Factor Authentication

**Goal:** Users can enable TOTP-based 2FA with recovery codes.

Deliverables:
- TOTP setup (QR code display, manual key)
- Confirm code to activate
- Recovery code generation and display
- 2FA login challenge gate (pending-challenge state between login and full token)
- Login with recovery code
- Disable 2FA
- Regenerate recovery codes
- shadcn/ui components for 2FA UI
- `docs/implementation-steps/0009-two-factor-auth.md`

---

## Milestone 10 — Product Schema

**Goal:** Database models for the core TrainAudit product (no AI yet).

Deliverables:
- Fitness profile model/migration
- Program audit model/migration
- Workout program + versions model/migration
- Workout days + exercise prescriptions model/migration
- Workout logs + log sets model/migration
- `docs/implementation-steps/0010-product-schema.md`

---

## Milestone 11 — Mock AI Layer

**Goal:** AI provider interface with a working mock. No real API calls.

Deliverables:
- `AiProvider` interface
- `MockAiProvider` (returns deterministic fixture responses)
- `OpenAiProvider` stub (wired but not called)
- JSON schemas for AI response shapes
- Mock response validation
- `docs/implementation-steps/0011-mock-ai-layer.md`

---

## Milestone 12 — First Product Flow (Audit)

**Goal:** User pastes a workout plan and gets an AI audit score (mocked).

Deliverables:
- Audit Workout Plan page
- Submit to API
- API returns mocked audit score
- Result displayed with shadcn/ui components
- "Fix and Convert" button (stubbed)
- `docs/implementation-steps/0012-first-product-flow.md`

---

## Milestone 13 — Fix and Convert Flow

**Goal:** Mocked audit converts into a structured trackable program.

Deliverables:
- Convert audit → program records
- Program detail page
- Active program state
- `docs/implementation-steps/0013-fix-and-convert.md`

---

## Milestone 14 — Workout Logging

**Goal:** User can log a workout session against their active program.

Deliverables:
- Current week view
- Workout detail
- Start workout
- Log sets
- Save workout log
- Workout history
- `docs/implementation-steps/0014-workout-logging.md`

---

## Milestone 15 — Weekly Review

**Goal:** User sees a weekly summary with mocked AI recommendations.

Deliverables:
- Deterministic weekly stats
- Mock AI summary
- Recommendations display
- `docs/implementation-steps/0015-weekly-review.md`

---

## Milestone 16 — Real AI Integration

**Goal:** Wire real OpenAI calls to replace the mock provider.

Deliverables:
- OpenAI provider fully implemented
- Environment variables for API key
- Usage limits
- Input length limits
- Retry/repair for invalid JSON responses
- Request logging
- `docs/implementation-steps/0016-real-ai-integration.md`

---

## Milestone 17 — MVP Polish

**Goal:** App is ready for first real users.

Deliverables:
- Empty states
- Loading states
- Error states
- Security review
- Basic test coverage
- Deployment prep
- `docs/implementation-steps/0017-mvp-polish.md`
