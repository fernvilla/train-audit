# TrainAudit Design System

## Status

**Placeholder** — component foundation will be installed in Milestone 3.

This document describes the intended design approach. It will be updated after Milestone 3 when shadcn/ui is configured and the first components are added.

---

## Why shadcn/ui

shadcn/ui is not a component library in the traditional sense. It is a collection of copy-paste-able, editable React components built on top of Radix UI (accessible primitives) and styled with Tailwind CSS.

**Chosen because:**
- Components live in the codebase — no black-box library to fight
- Every component is fully editable from day one
- Radix UI handles accessibility (keyboard nav, ARIA, focus management) so we don't have to
- Pairs naturally with Tailwind; no fighting two styling systems
- High-quality starting point for common UI patterns (forms, dialogs, dropdowns, toasts)
- Large ecosystem; well-documented

**What it is not:**
- A locked-in UI library (we own the code)
- A replacement for design thinking (we still define our own tokens and patterns)
- Something to install all at once (add components only when a milestone needs them)

---

## Where Components Live

```
apps/web/
├── components.json                    shadcn/ui config
├── src/
│   ├── components/
│   │   ├── ui/                        shadcn/ui components (generated, then owned by us)
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   └── ...
│   │   └── layouts/                   App layout shells (not shadcn/ui)
│   │       ├── AppLayout.tsx
│   │       └── AuthLayout.tsx
│   ├── lib/
│   │   └── utils.ts                   cn() helper (clsx + tailwind-merge)
│   └── styles/
│       └── globals.css                Tailwind directives + CSS custom properties
```

---

## How Components Are Added

Components are added one milestone at a time, only when actually needed.

```bash
# From apps/web
pnpm dlx shadcn@latest add button
pnpm dlx shadcn@latest add input
```

Each `add` command copies the component source into `src/components/ui/`. From that point it is our code. We can modify it freely.

**Rule:** Do not run `shadcn add` speculatively. If a milestone doesn't use a component, don't add it.

---

## First Components (Milestone 3)

These are needed for the app shell, landing page, and auth pages:

| Component | Use |
|---|---|
| `button` | All CTAs, form submits |
| `input` | Form fields |
| `label` | Form labels |
| `card` | Content containers |
| `form` | Form structure + error display |
| `alert` | Error/success messages |
| `separator` | Visual dividers |
| `badge` | Tags, status indicators |
| `skeleton` | Loading placeholders |

---

## Later Components (Added Per Milestone)

| Component | Milestone |
|---|---|
| `dialog` | M9 (2FA setup modal) |
| `dropdown-menu` | M6 (user menu) |
| `tabs` | M13 (program detail) |
| `table` | M14 (workout log) |
| `progress` | M15 (weekly review) |
| `avatar` | M7 (profile) |
| `sheet` | TBD (mobile nav) |
| `tooltip` | TBD |
| `sonner` (toast) | M6+ (auth feedback) |

---

## TrainAudit Color Direction

These are design intentions, not finalized tokens. Exact values set in Milestone 3.

| Role | Direction |
|---|---|
| Primary | Bright blue (`#2563EB` range) |
| Primary dark | Navy (`#1E3A5F` range) |
| Background | White / light gray (`#F8FAFC`) |
| Surface | White (`#FFFFFF`) |
| Border | Light gray (`#E2E8F0`) |
| Text primary | Near-black (`#0F172A`) |
| Text muted | Medium gray (`#64748B`) |
| Destructive | Red (`#DC2626`) |
| Success | Green (`#16A34A`) |

shadcn/ui uses CSS custom properties (`--primary`, `--background`, etc.). We will map TrainAudit colors to these properties in `globals.css`.

---

## Typography Direction

- **Font:** System font stack for now (fast, no external deps). May add Inter later.
- **Scale:** Tailwind default type scale.
- **Hierarchy:** Clear `text-sm`, `text-base`, `text-lg`, `text-xl`, `text-2xl` usage — no arbitrary sizes.

---

## Layout Approach

- **Mobile-first** — Tailwind's responsive prefixes (`md:`, `lg:`) layer in desktop styles
- **Max content width:** `max-w-7xl` with `px-4 sm:px-6 lg:px-8` gutters
- **App layout:** Fixed sidebar on desktop, bottom nav on mobile (introduced in Milestone 6+)
- **Auth layout:** Centered card, no nav

---

## Component Guidelines

**Do:**
- Compose shadcn/ui primitives into page-level components
- Keep `src/components/ui/` as the unmodified (or lightly modified) layer
- Build page-specific compositions in route files or `src/components/` subdirectories

**Don't:**
- Customize `globals.css` color tokens until Milestone 3 is done
- Add custom component variants before the base tokens are set
- Import from shadcn/ui package — components live in `src/components/ui/`, not a node_module

---

## Tailwind Configuration

Tailwind v4 (used with TanStack Start) configures via CSS (`@theme`) rather than `tailwind.config.js`. Configuration details will be documented in Milestone 3.

---

## What Not to Customize Too Early

- Don't override shadcn/ui default border radius until the design direction is confirmed
- Don't add a custom font until the base layout is working
- Don't build a full design token system before Milestone 3 — over-engineering the theme before seeing real components wastes time
- Don't add dark mode support before MVP — it can be layered in later

---

## Related Documents

- `docs/decisions/0002-shadcn-ui-component-foundation.md`
- `docs/implementation-steps/0003-web-app-shadcn-foundation.md` (created in Milestone 3)
