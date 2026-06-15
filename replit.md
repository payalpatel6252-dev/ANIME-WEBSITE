# AN TOONS

AN TOONS is an anime streaming website with a Netflix-style dark UI, featuring a homepage, watch pages, and an admin panel to manage anime and episodes.

## Run & Operate

- `pnpm --filter @workspace/antoons run dev` — run the frontend (port auto-assigned)
- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS v4
- API: Express 5
- Data: `artifacts/api-server/data/anime.json` (file-based, no database needed)
- Validation: Zod (`zod/v4`)
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — API contract source of truth
- `artifacts/api-server/data/anime.json` — anime data storage (read/write by the API)
- `artifacts/antoons/src/` — frontend React app
- `artifacts/antoons/src/pages/AdminPage.tsx` — admin panel (no login required)
- `artifacts/antoons/src/pages/WatchPage.tsx` — watch/episode page
- `artifacts/api-server/src/routes/anime.ts` — CRUD routes for anime + episodes

## Architecture decisions

- File-based JSON storage (no database) — simple, portable, persists across restarts
- Admin panel at `/admin` route (click "Admin" button in navbar) — no login required
- Episode embed via iframe (embedUrl field), with optional Telegram Post ID link
- API server stores `anime.json` one level above `dist/` at `artifacts/api-server/data/`
- OpenAPI-first: all types generated from `openapi.yaml` via Orval

## Product

- Homepage with hero banner (auto-rotating), genre filters, Trending/Popular/Latest sections
- Watch page per anime with episode list, iframe embed player, Telegram links
- Admin panel: full CRUD for anime (title, poster, banner, description, genres, rating, status) and episodes (number, title, Telegram post ID, embed URL)
- Adding an anime automatically makes it appear in homepage cards and Latest section

## Gotchas

- After editing the OpenAPI spec, always run `pnpm --filter @workspace/api-spec run codegen`
- `anime.json` lives at `artifacts/api-server/data/anime.json` (not `src/data/`) — that's what the running server reads/writes
- The API server must be restarted after code changes (it builds via esbuild then runs)
