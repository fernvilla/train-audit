# ADR 0002 — shadcn/ui as Component Foundation

**Status:** Accepted

---

## Context

The web app needs a UI component foundation that:

1. Covers common patterns (forms, buttons, inputs, dialogs, dropdowns, toasts) without building from scratch
2. Is accessible by default (keyboard navigation, ARIA, focus management)
3. Integrates cleanly with Tailwind CSS
4. Is not a locked-in black box — components should be ownable and editable
5. Fits a clean SaaS dashboard aesthetic
6. Doesn't require installing dozens of dependencies or committing to an opinionated design token system early

---

## Decision

Use **shadcn/ui** as the component foundation for `apps/web`.

shadcn/ui is not a traditional component library. It is a CLI tool that copies component source code into the project. Each component becomes owned code — it lives in `src/components/ui/`, is editable, and has no runtime library dependency.

Components are built on:
- **Radix UI** — accessible, unstyled primitives (handles keyboard nav, ARIA, focus traps)
- **Tailwind CSS** — utility-first styling
- **class-variance-authority (cva)** — variant-based component APIs
- **clsx + tailwind-merge** — safe class merging

---

## How Components Are Added

```bash
# From apps/web
pnpm dlx shadcn@latest add button
pnpm dlx shadcn@latest add input
```

This copies source files into `src/components/ui/`. The component is now ours to edit.

**Rule:** Add components only when a milestone actually needs them. Never speculatively install.

---

## Component Addition Schedule

| Component | Milestone | Reason |
|---|---|---|
| button, input, label, card, form, alert, separator, badge, skeleton | M3 | App shell, landing, auth forms |
| dropdown-menu | M6 | User nav menu |
| sonner (toast) | M6 | Auth feedback |
| dialog | M9 | 2FA setup |
| avatar | M7 | Profile page |
| tabs | M13 | Program detail |
| table | M14 | Workout log |
| progress | M15 | Weekly review |

---

## Alternatives Considered

### Headless UI (by Tailwind Labs)

- Also unstyled primitives; pairs with Tailwind
- Rejected: smaller component coverage than Radix UI; shadcn/ui's copy-paste model gives us more out of the box with less wiring

### Mantine

- Full-featured component library with excellent DX
- Rejected: own styling system conflicts with Tailwind; customization requires learning Mantine's theming; locked-in dependency

### Chakra UI

- Popular, accessible, good DX
- Rejected: CSS-in-JS model; more complex theming; not Tailwind-native; adds bundle weight

### MUI (Material UI)

- Massive ecosystem, well-documented
- Rejected: Material Design aesthetic requires heavy overriding for a clean SaaS look; CSS-in-JS; heavyweight

### Build from scratch with Radix UI directly

- Maximum control; no shadcn/ui overhead
- Rejected: shadcn/ui's components are already high quality Radix wrappers; no benefit to rebuilding them; shadcn/ui components are ours once copied, so there is no lock-in cost

---

## Consequences

**Positive:**
- Zero runtime dependency on a UI library (components are source code in the repo)
- Accessible primitives (Radix) with no extra effort
- Clean Tailwind integration; no style conflicts
- Easy to customize: change the source file
- TrainAudit color tokens slot into shadcn/ui's CSS custom property system cleanly
- Large community; good documentation; components are well-tested patterns

**Negative / Trade-offs:**
- Each component must be explicitly added (not a one-import solution)
- Updates require re-running `shadcn add` or manually applying diffs
- CSS custom property theming (`--primary`, `--background`, etc.) requires understanding shadcn/ui's token naming
- Some components are complex (e.g., `combobox`) and require significant customization for edge cases

---

## Related

- `docs/architecture/design-system.md`
- `docs/decisions/0001-monorepo-adonis-api-tanstack-start.md`
