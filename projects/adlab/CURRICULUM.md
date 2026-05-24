---
created: 2026-05-24
type: learning-curriculum
project: adlab
status: ready-to-execute
design_source: ~/Vaults/CROMaxLabs/spy-baby-spy/plans/2026-03-30-dashboard-design.md
schema_source: ~/Vaults/CROMaxLabs/Second Brain/Inbox/2026-05-24-adlab-supabase-schema-snapshot.md
spec: ~/Vaults/CROMaxLabs/docs/superpowers/specs/2026-05-22-dev-learning-plan-design.md
---

# Adlab — Curriculum

A read-side ad-intelligence dashboard on the existing Supabase project `fliqklclucdhjemdjatr` (507 ads, 21 tables, pgvector + tsvector + JSONB). Built atom-by-atom, layer-by-layer.

**Design comes from** [[spy-baby-spy/plans/2026-03-30-dashboard-design.md]] — adapted for ads instead of CRO pages. The page list, component library, and design language are already specified there. This curriculum builds them.

**Stack:** Next.js 14 (app router) + Prisma (introspects Supabase schema) + tRPC (typed API) + Recharts (charts) + Supabase JS client + Tailwind (dark theme).

---

## Layer 0 — Environment Setup (1 atom, full)

### Atom 0.1 — Bootstrap the Next.js project

**Time:** 30-45 min. Done once.

**Concept (B):**
A modern web app is a folder of code that runs on Node.js and serves HTML/JS to the browser. Next.js bundles the backend + frontend in one project — pages live in `app/`, server APIs live in `app/api/`. We start with the scaffold and immediately deploy a "Hello adlab" so the repo is alive on GitHub from minute one.

**Action (C):**

```bash
cd ~/Projects/dev-learning/projects/adlab

# 1. Install Next.js into this folder (TypeScript + Tailwind + app router + ESLint)
npx create-next-app@latest . --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"

# 2. Install Prisma + tRPC + Supabase client
npm install @prisma/client @trpc/server @trpc/client @trpc/react-query @trpc/next @tanstack/react-query zod
npm install --save-dev prisma

# 3. Add your Supabase credentials to .env.local (NEVER commit this)
cat > .env.local <<EOF
NEXT_PUBLIC_SUPABASE_URL="https://fliqklclucdhjemdjatr.supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="<paste-from-supabase-dashboard-settings-api>"
DATABASE_URL="<paste-direct-postgres-url-from-supabase-settings-database>"
EOF

# 4. Confirm .env.local is gitignored (it should be — Next.js adds it by default)
grep "\.env" .gitignore

# 5. Run the dev server
npm run dev
# Open http://localhost:3000 — see the Next.js welcome page

# 6. Commit + push
git add .
git commit -m "[layer0] bootstrap: Next.js + Prisma + tRPC scaffold"
git push
```

**Interview line:**
> "A Next.js project is a folder where `app/page.tsx` is the home route, `app/api/.../route.ts` is a server endpoint, and `npm run dev` serves it locally with hot reload. The .env.local file holds secrets that never go to git."

**Checkpoint:** http://localhost:3000 renders Next.js welcome. `git log` shows one commit on GitHub.

**Updates required after this atom:**
- `_progress.md` — mark Layer 0 done, set Layer 1 as next
- `_decisions.md` — log the stack choice (Next.js over Express, etc.)

---

## Layer 1 — How the web works (2 atoms, full)

### Atom 1.1 — First HTTP request against your real Supabase

**Time:** 15-20 min.

**Concept (B):**
HTTP is the language clients and servers use. A request has a URL, a method (GET to read, POST to write), headers (metadata like auth), and sometimes a body. Supabase exposes a REST API at `<project-url>/rest/v1/<table>`. We're going to talk to it directly with curl — no library, no abstraction — to see exactly what comes back.

**Action (C):**

```bash
# In the Shell pane:
source .env.local  # makes vars available
curl -i "$NEXT_PUBLIC_SUPABASE_URL/rest/v1/ads?select=id,brand_name,hook,performance_score&limit=3" \
  -H "apikey: $NEXT_PUBLIC_SUPABASE_ANON_KEY" \
  -H "Authorization: Bearer $NEXT_PUBLIC_SUPABASE_ANON_KEY"
```

**What to notice:**
- `HTTP/2 200` — the status line (200 = OK)
- Response headers (`content-type: application/json; charset=utf-8`)
- Blank line separates headers from body
- The JSON body — 3 real ads with id, brand_name, hook, score
- The `apikey` header is HOW Supabase knows who you are

**Interview line:**
> "I queried a Supabase REST endpoint directly with curl. The URL pattern is `/rest/v1/<table>?select=col,col&limit=N`, the apikey header authenticates anonymous read access, and the response is JSON. Status code, headers, body — same structure for every HTTP response."

**Checkpoint:** You see 3 real ads come back. You can point at the status code, the headers, and the body in the curl output.

**Commit:** `[layer1] first-api-call: curl Supabase ads endpoint`

---

### Atom 1.2 — Filter, sort, and limit via query params

**Time:** 15 min.

**Concept (B):**
Supabase's REST API exposes Postgres filters as URL query params: `?column=op.value`. Operators include `eq` (equals), `gt` (greater than), `like` (pattern), `in.(a,b,c)` (any of). Sorting is `?order=column.desc`. This is the read-side of the API you'll be wrapping in tRPC later.

**Action (C):**

```bash
# Top-10 highest-performing Smooche ads:
curl -s "$NEXT_PUBLIC_SUPABASE_URL/rest/v1/ads?brand_name=eq.Smooche&performance_score=gt.70&order=performance_score.desc&limit=10&select=id,hook,performance_score,days_active" \
  -H "apikey: $NEXT_PUBLIC_SUPABASE_ANON_KEY" | jq '.'

# Count of ads per brand (this requires a different RPC — try the simple count first):
curl -s "$NEXT_PUBLIC_SUPABASE_URL/rest/v1/ads?select=brand_name&brand_name=neq.null" \
  -H "apikey: $NEXT_PUBLIC_SUPABASE_ANON_KEY" \
  -H "Range: 0-0" \
  -H "Prefer: count=exact" -i | grep -i "content-range"
```

**Interview line:**
> "Supabase REST exposes Postgres operators as query params — `column=eq.value`, `column=gt.N`, `order=column.desc`. The `Prefer: count=exact` header returns total count in a Content-Range header. That's how pagination headers work in real APIs."

**Checkpoint:** You can build a curl URL that filters + sorts + limits, and you understand what each param does.

**Commit:** `[layer1] query-params: filter + sort + count via Supabase REST`

---

## Layer 2 — SQL + Your Real Schema (Index)

The 507 ads are real, the schema is sophisticated (pgvector, tsvector, JSONB), and your existing functions (`rag_candidates`, `match_ads`) are queryable. Each atom uses **real data**, not toy examples.

| # | Title | What you learn / build |
|---|---|---|
| **2.1** | Connect TablePlus to Supabase + explore | Schema browser — see all 21 tables, foreign keys, column types. No queries yet, just orientation. |
| **2.2** | SELECT basics with WHERE on `ads` | Find top-performing Smooche ads, ads missing `brand_id`, ads with hooks containing "hydration". |
| **2.3** | JOINs — find the 41 orphaned ads | `ads LEFT JOIN brands ON ads.brand_id = brands.id WHERE brands.id IS NULL`. Solves a real documented gap. |
| **2.4** | GROUP BY for analytics | Ads per brand, avg score per brand, count by funnel_stage. Foundation for the Dashboard page. |
| **2.5** | JSONB queries on `brands` | `brands.voice_config->>'tone'`, `brands.personas`. Postgres-specific syntax for nested JSON. |
| **2.6** | pgvector similarity search | Call `rag_candidates(query, k)` — your existing hybrid retrieval function. See semantic search return results. |
| **2.7** | Full-text search with `rag_context_tsv` | `WHERE rag_context_tsv @@ to_tsquery('english','urgency & guarantee')`. Postgres tsvector basics. |

**Total Layer 2:** ~7 atoms, ~150 min spread across 7-10 sessions.

---

## Layer 3 — Backend with Next.js + Prisma + tRPC (Index)

| # | Title | What you learn / build |
|---|---|---|
| **3.1** | `prisma db pull` — introspect existing schema | Prisma reads your Supabase schema and generates a TypeScript-typed client. Every table is now a `prisma.ads.findMany()` call with autocomplete. |
| **3.2** | First tRPC procedure — `ads.list` | A typed RPC endpoint. Client calls `trpc.ads.list.useQuery()`, server runs Prisma, types flow automatically end-to-end. |
| **3.3** | Add `ads.byId` and `ads.search` procedures | Filter args (Zod-validated), pagination, returns typed result. The pattern that will back every page. |
| **3.4** | Raw SQL through Prisma for vector queries | `prisma.$queryRaw` to call `rag_candidates()` — Prisma can't model pgvector natively, so you drop to raw SQL when needed. |
| **3.5** | Server-side env handling — anon key vs service-role | Service-role key NEVER reaches the client. Reserve it for Layer 7 mutations (fix-gap features). |
| **3.6** | Error handling + logging with Pino | Structured JSON logs to `logs/app.log`. The Logs pane in tmux tails this. |
| **3.7** | tRPC + Next.js API route wiring | `app/api/trpc/[trpc]/route.ts` — single endpoint serves all procedures. |

**Total Layer 3:** ~7 atoms, ~200 min.

---

## Layer 4 — APIs in Depth (Index)

| # | Title | What you learn / build |
|---|---|---|
| **4.1** | Supabase RLS basics | Row Level Security — anon key + RLS policies = safe public read. Why your anon key is OK in a public repo. |
| **4.2** | Read Supabase REST docs critically | The "PostgREST" layer. What's exposed, what's not. When to use it vs Prisma. |
| **4.3** | Bruno collection for your API | Save every tRPC endpoint as a Bruno test. Lives in `bruno/` in the repo. |
| **4.4** | Webhook signing (concept only) | If Supabase calls your app on data change, how do you verify the request is real? HMAC signing. Foundation for incoming webhooks. |
| **4.5** | Rate limiting and pagination | Why APIs paginate (large tables would OOM the client). How cursor-based vs offset-based pagination differ. |

**Total Layer 4:** ~5 atoms, ~120 min.

---

## Layer 5 — Frontend Pages (The Centerpiece — Index)

**Each page is adapted from [[spy-baby-spy/plans/2026-03-30-dashboard-design.md]].** Design specs already exist; you're implementing them.

### Component foundation (3 atoms)

| # | Title | Builds |
|---|---|---|
| **5.0a** | Sidebar (220px, dark, nav groups) | Component used by every page. Tailwind + dark theme tokens. |
| **5.0b** | TopBar + StatCard | Persistent top + reusable metric card. |
| **5.0c** | DataTable + FilterBar | The most important component. Sortable, filterable, URL-state-persisted. Built once, used 6+ times. |

### Pages (7 atoms)

| # | Route | Description |
|---|---|---|
| **5.1** | `/` Dashboard | Stats grid (ad count, brands, avg score, recent activity), alerts panel, top performers, category benchmarks. |
| **5.2** | `/ads` All Ads | The power table. Every column filterable: brand, score range, days active, ad_type, funnel_stage, has-screenshot, performance bucket. URL state. Slide-over panel on row click. |
| **5.3** | `/ads/[id]` Ad detail (slide-over + full page) | All fields rendered: hook, body, lightbulbs, embeddings preview, screenshot, similar ads via `rag_candidates`. |
| **5.4** | `/leaderboard` Performance Leaderboard | Top ads by `performance_score`, score distribution chart (Recharts), filterable by brand/format. |
| **5.5** | `/brands/[id]` Brand detail | JSONB fields rendered (voice_config, personas, color_palette). Ads grid for this brand. Score histogram. |
| **5.6** | `/patterns` Pattern Explorer | `pattern_definitions` + `ad_pattern_labels` joined. Click pattern → see member ads. |
| **5.7** | `/health` Data Quality | Orphan ads (the 41), unapplied embeddings (98), 0%-populated columns. Actionable list with one-click "fix" buttons (Layer 7 makes these real). |

**Total Layer 5:** ~10 atoms, ~300 min. **Densest layer because it's the centerpiece.**

---

## Layer 6 — Deploy (Index)

| # | Title | What you learn / build |
|---|---|---|
| **6.1** | One-click Vercel deploy from GitHub | Connect repo → Vercel auto-detects Next.js → preview URL on every push. |
| **6.2** | Environment variables in Vercel | Where `NEXT_PUBLIC_*` vs server-only secrets go. Why `SERVICE_ROLE_KEY` must be server-only. |
| **6.3** | Preview deploys vs production | Every PR gets its own URL. Main branch = production. |

**Total Layer 6:** ~3 atoms, ~75 min.

---

## Layer 7 — AI/LLM Integration (Index)

| # | Title | What you learn / build |
|---|---|---|
| **7.1** | `/search` Semantic Search page | UI for `rag_candidates()` — natural-language query → ranked ads with similarity scores. |
| **7.2** | VLM via qwen3-vl-30b — analyze a new ad screenshot | POST `/api/analyze`: send image URL → LM Studio at Mac Studio → structured JSON (hook, offer, CTA). |
| **7.3** | `/ask` natural-language Q&A | "Show me Smooche ads with countdown timers that score above 70" → LLM generates SQL → executes → returns ad list. |
| **7.4** | Fix-gap mutations (service-role) | The first WRITES: a button that re-attributes the 41 orphan ads to Smooche. Service-role key, server-only, audit log. |

**Total Layer 7:** ~4 atoms, ~120 min.

---

## GitHub Track (Parallel, ~5 atoms interspersed)

| # | When | Skill |
|---|---|---|
| **G.1** | Layer 0 | clone, branch, commit, push, PR — already practiced at bootstrap |
| **G.2** | Layer 3 | Open a real PR against `main` for a tRPC procedure, self-review the diff |
| **G.3** | Layer 5 | Resolve a merge conflict deliberately (rebase a feature branch onto updated main) |
| **G.4** | Layer 5 | `git blame` and `git log -p` for debugging your own code |
| **G.5** | Layer 7 | `git worktree` to work on two features in parallel without stash dance |

---

## Total scope (revised)

| Layer | Atoms | Time |
|---|---|---|
| 0 | 1 | ~45 min |
| 1 | 2 | ~30 min |
| 2 | 7 | ~150 min |
| 3 | 7 | ~200 min |
| 4 | 5 | ~120 min |
| 5 | 10 | ~300 min |
| 6 | 3 | ~75 min |
| 7 | 4 | ~120 min |
| GitHub | 5 (interspersed) | — |
| **Total** | **~44 atoms** | **~17 hours of active learning** |

At 20-min atoms in random pockets: **~6-8 weeks of calendar time** at a sustainable pace.

---

## Today's first session (~65 min)

1. Atom 0.1 — Bootstrap (~45 min) — installs, scaffold, push to GitHub
2. Atom 1.1 — First curl (~15 min) — see real ads come back
3. Commit and push

End state: working dev server, repo with multiple commits, you can read a JSON response and explain every field.

---

## Future layer expansion

Layers 2-7 are indexed here, not expanded. When you reach each, spawn a fresh session and ask:

> *"Expand Layer N atoms from [[adlab/CURRICULUM.md]]. Reference design from [[spy-baby-spy/plans/2026-03-30-dashboard-design.md]] and schema from [[Second Brain/Inbox/2026-05-24-adlab-supabase-schema-snapshot.md]]."*

That session will produce full atom-by-atom expansion (concept + action + interview line) without inheriting this conversation's token cost.

---

## 🔗 The Loom: Related Connections
- [[spy-baby-spy/plans/2026-03-30-dashboard-design.md]] — design source for every page in Layer 5
- [[Second Brain/Inbox/2026-05-24-adlab-supabase-schema-snapshot.md]] — schema reference for Layers 2-3
- [[thermoslim-platform/ad-intelligence/2026-05-11-supabase-db-gap-tracker.md]] — documented gaps that become Layer 5 (`/health`) and Layer 7 (`/api/fix-orphans`) features
- [[thermoslim-platform/roadmap/Dashboard Inventory.md]] — broader dashboard context across CROMaxLabs projects
- [[business-opportunities/opportunities/05-commerce-intelligence-dashboard-template.md]] — production-grade dashboard architecture reference
