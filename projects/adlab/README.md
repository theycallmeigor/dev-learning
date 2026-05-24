# adlab

Read-side ad intelligence dashboard on the existing Supabase project (`fliqklclucdhjemdjatr` — 507 ads, 21 tables, pgvector + tsvector + JSONB).

**What it does:** A multi-page Next.js app that lets you browse the ad corpus, see top performers per brand, drill into individual ads, run semantic search via the existing `rag_candidates()` Postgres function, and fix documented data-quality gaps (41 orphaned ads, 98 unapplied Twelve Labs embeddings, etc.).

**Why it exists:** anchor project for the dev-learning curriculum. Touches every layer (HTTP, SQL, backend, frontend, APIs, deploy, VLM/RAG) using your own real production data — far more meaningful than synthetic examples.

## Stack

- **Next.js 14** (app router, TypeScript, Tailwind) — frontend + backend in one repo
- **Prisma** — introspects the Supabase schema (`prisma db pull`), provides typed client
- **tRPC** — typed RPC layer between frontend and backend (no manual REST endpoints)
- **Recharts** — score distributions, brand benchmarks
- **Supabase JS client** — auth + RLS + storage
- **Pino** — structured JSON logging to `logs/`
- **Bruno** — API test collection in the repo

## Where to start

1. Read [CURRICULUM.md](./CURRICULUM.md) — the atom-by-atom build plan
2. Start at **Atom 0.1 — Bootstrap**
3. Update `_progress.md` after each atom

## Design source

Pages adapted from `~/Vaults/CROMaxLabs/spy-baby-spy/plans/2026-03-30-dashboard-design.md` — the spy-baby-spy dashboard spec is the design template; adlab is the ad-focused instance.

## Progress

See `_progress.md` for current status and next atom.
