---
created: 2026-05-24
type: learning-curriculum-expansion
project: adlab
status: optional-slot-in
parent: ./CURRICULUM.md
---

# Adlab — Expansion Pack

Optional atoms to slot into [CURRICULUM.md](./CURRICULUM.md) when interview signals or hitting a wall make them necessary. Demand-driven — don't run them in order, pull what you need when you need it.

Each atom marks its **prerequisite** ("slot anywhere after Layer N") and **interview signal** ("commonly asked in: …").

---

## Pack 1 — Foundation (TypeScript + React + Tailwind)

**When to pull from this pack:** as soon as Layer 3 or 5 feels like "fighting the syntax" instead of building features. These are the substrate the stack runs on.

### F.1 — TypeScript fundamentals (FULL ATOM)

**Slot:** anywhere after Layer 3.1 (first tRPC procedure) — when types start being a thing you write, not just consume.
**Time:** 25 min.
**Interview signal:** "Explain the difference between an interface and a type" — JS/TS shop opener.

**Concept (B):**
TypeScript is JavaScript with type annotations checked at *compile time*, not runtime. The compiler catches "I treated this string like a number" before the code runs. Three concepts do 80% of the work: **basic types** (`string`, `number`, `boolean`, `string[]`, `null | undefined`), **interfaces/types** (named shapes — `interface Ad { id: number; hook: string }`), and **type narrowing** (when TS figures out from `if (x !== null)` that `x` is no longer `null` inside that block).

**Action (C):**
1. Open `adlab/src/server/api/routers/ads.ts` (or wherever your first tRPC procedure lives).
2. Look at the procedure's input schema (Zod) and inferred output type — hover them in Cursor to see the inferred types.
3. Add a typed helper: `function formatAdRow(ad: { id: number; hook: string | null; score: number | null }): string { return ad.hook ?? 'no hook'; }`
4. Try to call it with the wrong shape. Watch the compiler complain.
5. Replace the inline type with `import { Ad } from '@prisma/client'` — now your helper uses the Prisma-generated type.

**Interview line:**
> "TypeScript catches type errors before runtime — wrong shapes, null handling, function signatures. With Prisma, the database schema generates types for me, so I get end-to-end safety: schema → Prisma → tRPC → React, all typed."

**Checkpoint:** You can explain what `string | null` means and why TypeScript forces you to handle the null case before treating it as a string.

**Commit:** `[expansion] ts-fundamentals: typed ad row helper`

---

### F.2-F.6 — Foundation Pack (Index)

| # | Title | Slot | Interview signal |
|---|---|---|---|
| F.2 | **Generics in tRPC** — `<TInput, TOutput>` patterns | After 3.3 | "How does tRPC give you end-to-end types?" |
| F.3 | **Zod schemas + type inference** — `z.infer<typeof schema>` | After 3.3 | "How do you validate API input?" Zod is the de-facto standard. |
| F.4 | **React props + useState** — first interactive component | Before 5.0a | "Walk me through a React component" — every junior screen. |
| F.5 | **useEffect for side effects (and when NOT to)** — fetching, subscriptions | After 5.3 | "When should you use useEffect?" trick question — answer: less than you think (use tRPC/React Query for data). |
| F.6 | **Tailwind utility-first + dark theme tokens** — replicate spy-baby-spy's `bg-[#0f1117]` aesthetic | Before 5.0a | Not asked directly, but visible in your demo. Polish signal. |

**Total Foundation Pack:** ~6 atoms, ~150 min.

---

## Pack 2 — Quality (Testing + Debugging + Error Handling)

**When to pull from this pack:** as soon as Layer 5 has shippable pages. Interviewers ask "where are the tests" within the first 10 minutes of a code review. Testing without something to test is fake; testing your own real code is gold.

### Q.1 — First unit test with Vitest (FULL ATOM)

**Slot:** after Layer 5.0c (DataTable component exists).
**Time:** 30 min.
**Interview signal:** "Show me how you'd test this function." Cannot fake this — interviewer can tell instantly if you've never written a test.

**Concept (B):**
A unit test is a function that calls another function with known inputs and asserts the output matches what you expect. The minimum harness: `import { test, expect } from 'vitest'`, write `test('name', () => { expect(actual).toBe(expected) })`. Tests run in isolation (no DB, no network) and fail loudly when behavior changes. They are also the safety net for refactors — pass before and after = your change was safe.

**Action (C):**
1. Install Vitest: `npm install --save-dev vitest`
2. Add `"test": "vitest"` to `package.json` scripts.
3. Pick a pure function from your DataTable component logic — likely a filter/sort helper. If you don't have one yet, extract one (e.g., `filterAdsByBrand(ads, brandName)`).
4. Create `src/lib/__tests__/filter-ads.test.ts`:
   ```ts
   import { test, expect } from 'vitest';
   import { filterAdsByBrand } from '../filter-ads';

   test('filters ads to a single brand', () => {
     const ads = [
       { id: 1, brand_name: 'Smooche' },
       { id: 2, brand_name: 'MDD' },
       { id: 3, brand_name: 'Smooche' },
     ];
     expect(filterAdsByBrand(ads, 'Smooche')).toHaveLength(2);
   });

   test('returns empty when no matches', () => {
     expect(filterAdsByBrand([], 'Smooche')).toEqual([]);
   });
   ```
5. Run `npm test`. Watch it pass.
6. Break the function (return `[]` for everything). Watch the test fail clearly.
7. Fix it. Commit.

**Interview line:**
> "I use Vitest because it's fast, integrates with Vite/Next.js, and the syntax is identical to Jest. My first test is always on a pure function — given input X, assert output Y. I write the test first when I'm confident about the expected output."

**Checkpoint:** `npm test` passes, you can break and fix a test deliberately.

**Commit:** `[expansion] vitest-first-test: filter-ads.test.ts passing`

---

### Q.2-Q.5 — Quality Pack (Index)

| # | Title | Slot | Interview signal |
|---|---|---|---|
| Q.2 | **Playwright E2E test for `/ads` page** — load page, assert table renders | After 5.2 | "Have you done end-to-end testing?" — Playwright is the 2026 standard. |
| Q.3 | **Mocking Prisma in tests** — test your tRPC procedures without a real DB | After Q.1 | "How do you test database code?" |
| Q.4 | **Chrome DevTools workflow** — Network tab for tRPC calls, Console for errors, Sources for breakpoints | First time you hit a bug in Layer 5 | "How would you debug a slow page?" — DevTools mastery is daily FDE work. |
| Q.5 | **Reading a stack trace + structured error handling** — wrap tRPC procedures in try/catch, surface `TRPCError` with codes | After 3.6 (Pino logging) | "What happens when the DB call fails?" — answer with codes, not silence. |

**Total Quality Pack:** ~5 atoms, ~150 min.

---

## Pack 3 — Reading (Other People's Code)

**When to pull from this pack:** anywhere. This skill is decorrelated from your build progress — you can do it at week 1 or week 8.

### R.1 — Read a real production Next.js codebase (FULL ATOM)

**Slot:** anywhere after Atom 0.1. Ideal: between Layer 3 and Layer 5 (you've seen the stack basics, now see how a real team uses them).
**Time:** 45-60 min (worth blocking out a full session).
**Interview signal:** "Have you worked in a large codebase before?" — being able to *talk fluently* about reading code is the single biggest FDE differentiator.

**Concept (B):**
Reading code you didn't write is the most common FDE/Solutions Engineer activity. Customers hand you their codebase, often poorly documented, and your job is to understand it well enough to extend or debug it. The skill is **orientation, not memorization** — you build a mental map of "where is auth? where are the API routes? where do they query the DB? what's the convention for handling errors?" — without reading every line.

**Action (C):**
Pick one of these open-source Next.js projects (all production-grade, all use tRPC or similar):

- **Cal.com** — `github.com/calcom/cal.com` — Open source Calendly. Large, real-world, prisma + tRPC + Next.js.
- **Dub.co** — `github.com/dubinc/dub` — URL shortener with analytics. Mid-size, modern stack.
- **create-t3-app starter** — `github.com/t3-oss/create-t3-app` — Smaller, opinionated starter exemplifying the stack.

For your chosen repo:

1. Clone it into `~/Projects/reading/` (not adlab):
   ```bash
   mkdir -p ~/Projects/reading && cd ~/Projects/reading
   git clone https://github.com/dubinc/dub.git
   cd dub
   ```
2. **First pass — structural orientation** (15 min):
   - Open the README. Skim for what the project does and the stack it uses.
   - Look at the top-level folder structure: `apps/`, `packages/`, `src/`. What lives where?
   - Open `package.json` — read the dependencies. What's familiar (Next.js, tRPC, Prisma, Tailwind) vs new (e.g., Drizzle ORM, NextAuth, Upstash)?
   - Find one or two key entry points: `app/page.tsx`, `server/api/`, `prisma/schema.prisma`.

3. **Second pass — pick a feature, trace it end to end** (30 min):
   - Pick a small feature you can identify (e.g., "creating a short link"). 
   - Find the UI component, the API endpoint it calls, and the DB function. Trace the path with `git grep`.
   - Note the patterns: how do they handle errors? How do they validate input? How is auth checked?

4. **Write a "reading note" in `_atoms/R-1-reading-dub.md`**:
   - 5 bullets on what they do well
   - 5 bullets on patterns I'd steal for adlab
   - 3 questions I'd ask in a code review

**Interview line:**
> "I read open-source production Next.js codebases regularly — Cal.com, Dub.co, the T3 starter. The skill isn't memorizing them — it's orienting fast. I look at the folder structure, package.json, and one feature traced end-to-end. That gives me 80% of the mental map an FDE needs on day 1 with a new customer."

**Checkpoint:** You can describe one feature of the project from UI through API to DB, in 3-4 sentences, without re-opening the code.

**Commit (in adlab repo):** `[expansion] read-dub: reading notes from dub.co architecture`

---

### R.2-R.4 — Reading Pack (Index — optional, do as time allows)

| # | Title | Slot |
|---|---|---|
| R.2 | **Read the Supabase JS client source** — understand what `supabase.from('ads').select()` actually does | Anywhere after Layer 3 |
| R.3 | **Read your own diff before pushing** — `git diff` and code-review your own PR | Habit-forming, after Layer 5 |
| R.4 | **Read tRPC's source for one router function** — understand the type magic | After F.2 (generics) |

**Total Reading Pack:** ~4 atoms, ~150 min.

---

## How to use this pack

1. **Don't run it sequentially.** Pull atoms when:
   - You hit a wall in the core curriculum and feel the gap (e.g., "I don't really understand useEffect, let me do F.5")
   - An interview surfaces a gap ("they kept asking about testing → do Q.1 next")
   - You want a low-cognitive-load session (Reading atoms are perfect for tired hours)

2. **Update `_progress.md`** when you complete an expansion atom — same format as core atoms, just prefixed with the pack code (F.1, Q.1, R.1).

3. **Total available expansion:** ~15 atoms, ~7-8 additional hours of learning. Adding all three packs would extend the project from ~17 hours to ~25 hours total.

---

## 🔗 The Loom: Related Connections
- [[CURRICULUM.md]] — the core 44 atoms this pack extends
- [[~/Vaults/CROMaxLabs/spy-baby-spy/plans/2026-03-30-dashboard-design.md]] — design source; many F.* atoms reference these tokens
- [[Second Brain/Inbox/2026-05-24-adlab-supabase-schema-snapshot.md]] — schema reference for Q.* atoms that touch the DB
