# Decisions — vox

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

### 2026-05-24 — Ship-first mode, not layer-by-layer mode

**Decision:** vox uses **sprints** (shippable increments) instead of layers (abstract understanding gates). Each sprint ends with something the client could use.
**Alternatives considered:** the same 7-layer curriculum used in adlab.
**Why:** The client is using this for real research. Spending 4 hours hand-rolling OAuth from raw `requests` before delivering a CSV is professional malpractice. Better to ship Sprint 0 today with PRAW, then dig into wire-level details laterally as questions arise. Adlab is a personal learning project; vox is a client deliverable. Different shapes for different goals.
**Tradeoffs accepted:** Less guaranteed depth in any one area. Mitigated by Deepening Pack atoms pulled on-demand.

### 2026-05-24 — Stack: Python + PRAW

**Decision:** Python 3.12 + PRAW + pandas for Reddit; Playwright (Python bindings) for TikTok later.
**Alternatives considered:** Node.js (consistent with adlab/ThermoSlim) + snoowrap. Also: pure `requests` no library.
**Why:** PRAW is the de-facto Reddit client — every tutorial assumes it. pandas writes CSVs in one line. Python's scraping ecosystem (BeautifulSoup, Playwright, Polars) is far richer than JS. Stack inconsistency with adlab is fine — every FDE/SE role in 2026 wants both Python and TypeScript proficiency; this project teaches Python while adlab teaches TS.
**Tradeoffs accepted:** Two languages in the brain at once. Mitigated by the fact that this is the actual industry expectation.

### 2026-05-24 — PRAW over raw requests (initially)

**Decision:** Use PRAW from Sprint 0. Hand-roll the underlying HTTP later in Deepening atoms (D.1, D.2, D.3).
**Alternatives considered:** Start with raw `requests`, build up understanding before reaching for a library.
**Why:** Ship-first mode. PRAW handles OAuth, pagination, and rate limits transparently — getting the same behavior with raw `requests` takes a session of its own. Better to deliver a CSV by end of session 1 and revisit the abstractions when a real question arises ("why did PRAW rate-limit me there?").
**Tradeoffs accepted:** Initial sessions feel more like "magic." Mitigated by explicit Deepening atoms.

### 2026-05-24 — CSV output before SQLite/Postgres

**Decision:** Sprint 0-1 outputs to flat CSVs in `data/`. Idempotency via SQLite arrives in Sprint 2. Supabase write-through is future (Sprint 5).
**Alternatives considered:** Write directly to the adlab Supabase from day 1.
**Why:** Client wants something they can open in Excel. CSVs are universal. Adding a DB before they ask for a DB is overengineering. Each future format (SQLite → Postgres) earns its place when the simpler one stops working.
**Tradeoffs accepted:** CSVs will get unwieldy at scale; we'll know when, and graduate then.
