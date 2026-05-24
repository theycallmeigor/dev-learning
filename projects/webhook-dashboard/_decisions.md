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

### 2026-05-22 — Anchor project: webhook integration dashboard

**Decision:** build a webhook receiver + database + API + minimal dashboard.
**Alternatives considered:** todo app, blog engine, e-commerce demo.
**Why:** mirrors FDE/Solutions Engineer work (integrating customer systems, handling webhooks, querying data). Demo-able in interviews as "I built this — here's the repo."
**Tradeoffs accepted:** less visible "wow factor" than a polished UI demo.

### 2026-05-22 — Stack: Node.js + Express + PostgreSQL (local) + plain HTML/JS frontend

**Decision:** Node + Express + local Postgres, frontend is plain HTML/JS (no React).
**Alternatives considered:** Python + FastAPI, Next.js full-stack, SQLite instead of Postgres.
**Why:** Node is closest to what I already touch (ThermoSlim, MasterApp). Express is the most-used Node framework — wide interview relevance. Postgres because Supabase uses it, and FDE roles often touch it. Plain HTML/JS frontend forces me to understand fetch/DOM before reaching for a framework.
**Tradeoffs accepted:** plain JS frontend is uglier than React; no full-stack framework convenience.

### 2026-05-22 — Two agents (Architect + Mechanic), not one

**Decision:** use tmux-ide agent teams to split learning into two agents — Architect for concepts, Mechanic for code.
**Alternatives considered:** single Claude session for everything, or three agents (add Reviewer).
**Why:** mixing "explain the concept" with "fix this error" in one conversation loses the thread. Two cognitive modes = two agents. Reviewer adds value later but is overhead now.
**Tradeoffs accepted:** more panes to manage on a 16" screen; potential context fragmentation between agents.
