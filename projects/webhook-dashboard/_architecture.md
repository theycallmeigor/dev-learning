# Architecture

The connection map. Update this as the project grows. By Layer 5 you should be able to redraw it from memory.

---

## Current state — Layer 0 (pre-build)

```
( nothing built yet — diagram appears as you build )
```

---

## Target state — by end of Layer 7

```
                     ┌──────────────────┐
                     │  External source │
                     │  (Shopify / mock)│
                     └────────┬─────────┘
                              │ webhook (HTTP POST)
                              ▼
   ┌──────────────────────────────────────────┐
   │           Node.js + Express              │
   │                                          │
   │   POST /webhooks  →  validate            │
   │                   →  log to logs/        │
   │                   →  insert into DB      │
   │                                          │
   │   GET  /events    →  query DB → JSON     │
   │                                          │
   │   POST /ask       →  query DB +          │
   │                      Claude API → answer │
   └─────────────┬──────────────────┬─────────┘
                 │                  │
                 ▼                  ▼
        ┌─────────────────┐   ┌──────────────┐
        │   PostgreSQL    │   │  Claude API  │
        │   events table  │   │  (Layer 7)   │
        └─────────────────┘   └──────────────┘
                 ▲
                 │ fetch
                 │
   ┌─────────────┴────────────────────────────┐
   │   Frontend (plain HTML + JS)             │
   │   fetches /events, renders dashboard     │
   └──────────────────────────────────────────┘
```

---

## Layer-by-layer evolution

Update as each layer completes:

- **Layer 1 (HTTP):** _(not built)_
- **Layer 2 (SQL):** _(not built)_
- **Layer 3 (Backend):** _(not built)_
- **Layer 4 (APIs):** _(not built)_
- **Layer 5 (Frontend):** _(not built)_
- **Layer 6 (Deploy):** _(not built)_
- **Layer 7 (AI/LLM):** _(not built)_
