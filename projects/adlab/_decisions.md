# Decisions

Why this stack, why this pattern, why not the obvious alternative. Interview answers pre-written.

## Format

```
### YYYY-MM-DD — <decision title>

**Decision:** what you chose
**Alternatives considered:** what you ruled out
**Why:** the reasoning (in your own words)
**Tradeoffs accepted:** what you're giving up
```

---

## Entries

### 2026-05-24 — Anchor project: adlab (read-side ad intelligence dashboard on existing Supabase)

**Decision:** build a read-side multi-page dashboard on the existing `fliqklclucdhjemdjatr` Supabase project (507 ads, 21 tables, pgvector + tsvector + JSONB).
**Alternatives considered:** synthetic webhook-dashboard (original 2026-05-22 plan), todo app, e-commerce demo, brand-new ingestion pipeline.
**Why:** Read-side tooling on a real existing DB is closer to actual FDE/SE day-1 work than ingestion. Real data is meaningful. Interview story is stronger ("I built a dashboard on a production ad-intelligence system"). Schema sophistication means every advanced concept (vector similarity, JSONB, tsvector, RLS) is already there to learn from.
**Tradeoffs accepted:** must respect RLS, service-role key handling becomes a real concern in Layer 7.
**Supersedes:** the 2026-05-22 webhook-dashboard decision.

### 2026-05-24 — Stack: Next.js + Prisma + tRPC + Recharts + Supabase JS

**Decision:** Next.js 14 (app router, TypeScript, Tailwind) + Prisma (`db pull` reads existing schema) + tRPC + Recharts + Supabase JS client.
**Alternatives considered:** Express + plain HTML/JS (original plan), Python + FastAPI + Jinja, Supabase auto-generated UI.
**Why:** Frontend-heavy scope demands a real framework. Next.js gives file-based routing (multi-page free), full-stack in one repo, Vercel one-click deploy, matches ThermoSlim/MasterApp patterns already in use. tRPC = end-to-end typed RPC, no manual REST. Prisma reads the 21-table schema without manual modeling. This is the **dominant 2026 FDE/SE stack** — interview table-stakes.
**Tradeoffs accepted:** more concepts to learn upfront (JSX, hooks, tRPC routers) but each is interview-relevant.
**Supersedes:** the 2026-05-22 "Node + Express + plain HTML/JS" decision.

### 2026-05-24 — Design source: spy-baby-spy dashboard spec

**Decision:** adopt `~/Vaults/CROMaxLabs/spy-baby-spy/plans/2026-03-30-dashboard-design.md` as the design template — pages, components, keyboard shortcuts, dark-theme tokens are already specified there.
**Why:** I already did this design work for spy-baby-spy. Adlab is structurally the same application (read-side intelligence dashboard with sidebar nav + power tables + slide-over panels) with ads as the entity instead of CRO pages. Reuse saves weeks of design decisions and gives the project a real production-grade reference.

### 2026-05-22 — Two agents (Architect + Mechanic), agent-agnostic via runtime profiles

**Decision:** use tmux-ide agent teams. Architect = concepts/why. Mechanic = code/fixes. Role prompts live as portable system prompts in `_framework/agents/`. Four runtime profiles in `_framework/profiles/`: `local`, `cheap`, `balanced`, `premium`.
**Alternatives considered:** single agent for everything, three agents (add Reviewer), proprietary Claude-only stack.
**Why:** Mixing "explain the concept" and "fix the error" in one conversation loses the thread. Two cognitive modes = two agents. Portable prompts = no vendor lock-in (works with Claude Code, Antigravity, Aider+Kimi, Ollama, LM Studio). The `local` profile runs entire curriculum at $0 on Mac Studio.
**Tradeoffs accepted:** more panes to manage on a 16" screen; potential context fragmentation between agents (mitigated by hard role boundaries in prompts).
