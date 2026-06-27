# TrainAudit

> Turn any workout plan into a trackable, adaptive training program.

AI-powered workout plan auditor, program compiler, workout tracker, and adaptive training block manager.

---

## Monorepo Structure

```
trainaudit/
├── apps/
│   ├── api/          AdonisJS v7 API backend (port 3333)
│   └── web/          TanStack Start frontend (port 3000)
├── packages/
│   └── shared/       Shared TypeScript types (introduced when needed)
└── docs/             Architecture, decisions, and implementation steps
```

## Prerequisites

- [Node.js 24](https://nodejs.org/) via [nvm](https://github.com/nvm-sh/nvm)
- [pnpm 9+](https://pnpm.io/)
- [Docker](https://www.docker.com/) (for local PostgreSQL)

## Quick Start

```bash
nvm use 24
pnpm install
```

Local dev (both apps, after Milestone 4):

```bash
docker compose up -d        # start PostgreSQL
pnpm dev                    # starts API + web in parallel
```

Or individually:

```bash
pnpm api                    # API only
pnpm web                    # web only
```

## Documentation

See [`docs/`](./docs/) for architecture, decisions, and per-milestone implementation notes.

Start with [`docs/README.md`](./docs/README.md).
