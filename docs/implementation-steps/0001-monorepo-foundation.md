# Step 0001 — Monorepo Foundation

## Goal

Establish the pnpm workspace. Both app directories exist as valid packages. Node version is pinned. Root scripts wire up per-app dev commands.

## Summary

Created the monorepo scaffold: `pnpm-workspace.yaml`, root `package.json`, `.nvmrc`, `.gitignore`, root `README.md`, and placeholder `package.json` files for all three packages. No application code yet — just enough to confirm `pnpm install` and `pnpm ls -r` work.

## Why This Change Was Made

Everything in Milestone 2 and 3 depends on the workspace being wired first. AdonisJS and TanStack Start will be scaffolded into `apps/api` and `apps/web` — but those directories need to exist as workspace packages before those tools can scaffold into them cleanly.

Pinning Node 24 in `.nvmrc` is critical: AdonisJS v7's native TypeScript type-stripping requires ≥22.18; Vite 8 / Rolldown requires ≥20.19. Node 24 satisfies both with headroom.

## Files Created

```
.nvmrc
.gitignore
pnpm-workspace.yaml
package.json
README.md
apps/api/package.json
apps/web/package.json
packages/shared/package.json
docs/implementation-steps/0001-monorepo-foundation.md   ← this file
```

## Files Modified

None.

## Key Decisions

### pnpm as package manager

- Workspace linking is first-class in pnpm
- Strict node_modules layout prevents phantom dependency access
- Faster installs than npm; no hoisting surprises

### Root `package.json` scripts

- `pnpm api` — start API dev server
- `pnpm web` — start web dev server
- `pnpm dev` — start both in parallel (`--parallel`)
- `pnpm build` / `pnpm typecheck` — run across all apps

These use `--filter` to target specific workspaces. They won't work until Milestone 2/3 add real app dependencies and scripts, but the structure is correct now.

### `@trainaudit/*` package names

- `@trainaudit/api`, `@trainaudit/web`, `@trainaudit/shared`
- Scoped names allow `--filter '@trainaudit/*'` glob in root scripts
- `packages/shared` gets the same scope for future workspace linking

### `packages/shared` deferred

- Directory and `package.json` created now to satisfy workspace declaration
- No actual shared code until there's a real need (API types, validation schemas)
- Premature abstraction avoided

## How To Run

```bash
cd /path/to/train-audit
nvm use 24          # or: export NVM_DIR="$HOME/.nvm"; . "$NVM_DIR/nvm.sh"; nvm use 24
pnpm install
```

## How To Test Manually

```bash
nvm use 24
pnpm install
pnpm ls -r          # should list @trainaudit/api, @trainaudit/web, @trainaudit/shared
node --version      # should be v24.x.x
```

Expected output from `pnpm ls -r`:

```
@trainaudit/api 0.0.1
@trainaudit/web 0.0.1
@trainaudit/shared 0.0.1
```

## Known Gaps / Not Done Yet

- App placeholder `package.json` files have no scripts or dependencies — they are placeholders only
- `pnpm dev`, `pnpm api`, `pnpm web` will fail until Milestone 2/3 add real app scripts
- No TypeScript config yet (added per-app in M2/M3)
- No Docker Compose yet (Milestone 4)
- No `.env` files yet (Milestone 2 for API, Milestone 3 for web)

## Rollback Notes

Delete the files listed above. The `docs/` directory from M0 is unaffected.

## Next Suggested Step

**Milestone 2 — AdonisJS API App**

Replace `apps/api/package.json` placeholder with a real AdonisJS v7 API app. Configure TypeScript, add a `GET /health` endpoint, confirm `pnpm api` starts the server on port 3333.
