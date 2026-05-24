---
created: 2026-05-24
type: learning-curriculum
project: vox
mode: ship-first
status: ready-to-execute
parent_framework: ~/Projects/dev-learning/_framework/
---

# vox — Curriculum (Ship-First Sprints)

A voice-of-customer scraper for a real client. **Each sprint produces something the client could use.** Learning happens because you build real things, not before you build them.

The structure intentionally inverts adlab's: there, understanding gates progress. Here, **shipping gates progress**; understanding is the lateral parallel.

---

## Sprint 0 — Ship the dumb version (full atom)

### Atom 0.1 — Reddit → CSV in one script

**Time:** 90-120 min. **Deliverable:** a CSV the client can open in Excel.

**Goal:**
By the end, a single Python file pulls the latest 100 posts from a configured subreddit, writes them to `data/<subreddit>_<date>.csv` with columns: title, author, score, num_comments, created_utc, permalink, body. **You email it to the client and they use it.** Ugly. Hard-coded. Works.

**Action (C):**

```bash
cd ~/Projects/dev-learning/projects/vox

# 1. Python venv
python3.12 -m venv .venv
source .venv/bin/activate

# 2. Install only what you need today
pip install praw pandas python-dotenv

# 3. Get Reddit API credentials (one-time)
#    Go to https://www.reddit.com/prefs/apps → "create another app..." → script type
#    Note: client_id (under the app name), client_secret, your reddit username
#    No redirect URI matters for script-type apps; put http://localhost:8080

# 4. Save credentials to .env (gitignored)
cat > .env <<EOF
REDDIT_CLIENT_ID=...
REDDIT_CLIENT_SECRET=...
REDDIT_USERNAME=...
REDDIT_USER_AGENT="vox:v0.1 (by /u/your_username)"
EOF

# 5. Write src/scrape_subreddit.py — use the Mechanic agent
#    Have it write a script that:
#    - Loads .env
#    - Uses PRAW to fetch subreddit.hot(limit=100)
#    - Writes rows to data/{subreddit}_{YYYY-MM-DD}.csv using pandas

# 6. Run it
python src/scrape_subreddit.py r/skincare

# 7. Open the CSV in Numbers/Excel/whatever the client uses
open data/skincare_2026-05-24.csv

# 8. Commit (without the .env file — verify .gitignore is doing its job)
git add .
git commit -m "[sprint0] reddit-csv: first working scrape, single subreddit, 100 posts"
git push
```

**What you understand by the end:**
- A Python venv is an isolated package set — `source .venv/bin/activate` switches you into it
- PRAW = a Python wrapper around Reddit's REST API. It handles OAuth, rate limits, pagination. You'll dig into "what PRAW actually does" in a Deepening atom later — but for now, it just works.
- `.env` keeps secrets out of git. `python-dotenv` loads them into environment variables.
- A "subreddit" in PRAW is `reddit.subreddit('skincare')`. `.hot(limit=100)` returns the 100 hot posts. Each `submission` has `.title`, `.score`, `.author`, etc.
- pandas writes a CSV with one line.

**Interview line (after this single atom):**
> "I built a Reddit scraper in Python using PRAW. It pulls the hot posts from a configured subreddit, normalizes the fields, and writes a CSV. The API credentials are in a gitignored .env file loaded via python-dotenv."

**Checkpoint:** CSV opens in Excel, has the expected columns, contains real Reddit data. Repo on GitHub has the script (NOT the .env).

**Updates required after this atom:**
- `_decisions.md` — log "PRAW over raw requests for ship-first (Deepening atom D.1 will revisit)"
- `_progress.md` — Sprint 0 done, Sprint 1 ready

---

## Sprint 1 — Make it useful (Index)

**Goal:** the client can actually use it as a research tool — multiple subreddits, keyword filtering, comments not just posts, CLI control.

| # | Title | Ships |
|---|---|---|
| **1.1** | Multi-subreddit support via CLI arg | `python scrape.py r/skincare r/Sephora r/AsianBeauty` — one CSV per subreddit |
| **1.2** | Keyword filter — only include posts matching ANY of N keywords | `--keywords "vitamin c","niacinamide","tret"` filters at write time |
| **1.3** | Pull comments too — top N comments per post in a separate CSV | `data/skincare_comments_2026-05-24.csv` |
| **1.4** | Date range filter — only posts from last 30 days | `--since 30d` argument |
| **1.5** | Combined CSV with both posts and comments tagged by record_type | Single output the client opens once |

**Total Sprint 1:** ~5 atoms, ~3-4 hours.

---

## Sprint 2 — Reliability (Index)

**Goal:** runs without you babysitting it, handles failures, doesn't re-fetch what it already has.

| # | Title | Ships |
|---|---|---|
| **2.1** | Logging to `logs/vox.log` with structured timestamps | You can read what happened on any past run |
| **2.2** | Retry on transient errors (network, 5xx) with exponential backoff | Doesn't crash on a flaky connection |
| **2.3** | Idempotency — track scraped post IDs in SQLite, skip duplicates | Re-runs only fetch NEW content |
| **2.4** | Slack or email alert on failure | Client knows when scraping is broken |
| **2.5** | Pretty CLI output with progress bars (`rich` or `tqdm`) | Feels like a real tool |

**Total Sprint 2:** ~5 atoms, ~3 hours.

---

## Sprint 3 — Automation (Index)

**Goal:** runs on a schedule, client doesn't have to ask you for fresh data.

| # | Title | Ships |
|---|---|---|
| **3.1** | macOS launchd plist (or cron) — daily run at 6am | Fresh CSV waiting every morning |
| **3.2** | Auto-upload CSV to Google Drive folder client has access to | Client doesn't have to remember to ask |
| **3.3** | Weekly summary email — "this week scraped N posts across M subreddits, top keywords were X" | High-signal, low-noise comms |

**Total Sprint 3:** ~3 atoms, ~2 hours.

---

## Sprint 4 — TikTok extension (Index — harder, do last)

**Goal:** add TikTok comments as a second data source. Different beast — no API.

| # | Title | Ships |
|---|---|---|
| **4.1** | Install Playwright + first headless browser session | Playwright opens TikTok, takes screenshot |
| **4.2** | Navigate to a TikTok video URL + scroll the comments panel | Comments load into the DOM |
| **4.3** | Extract comments via Playwright's locator API | First TikTok CSV |
| **4.4** | Handle anti-bot signals — random delays, real user agent, occasional fail-then-retry | Robust enough for a daily scrape of a small video list |
| **4.5** | Combine Reddit + TikTok output into unified VoC dataset | Single CSV the client can analyze cross-platform |

**Total Sprint 4:** ~5 atoms, ~5-6 hours. **Plan for it being painful** — TikTok actively resists this.

---

## Deepening Pack — Lateral atoms (do when curious / blocked)

These are *not in the critical path*. Pull them when something breaks or your curiosity demands "but what is PRAW actually doing?"

| # | Title | Trigger |
|---|---|---|
| **D.1** | Replicate one PRAW call with raw `requests` | "How does PRAW authenticate?" |
| **D.2** | OAuth 2 client-credentials flow by hand | When a customer asks about token refresh in an interview |
| **D.3** | Read Reddit's `X-Ratelimit-*` response headers and write a sleep policy | When you hit rate limits in production |
| **D.4** | Walk one full PRAW pagination cycle with the `after` cursor | When pulling >1000 results and PRAW seems to stall |
| **D.5** | Parse a single complex post manually (markdown body, mention extraction, hashtags) | When the client asks for cleaner output |
| **D.6** | Read the PRAW source for `Subreddit.hot()` | Just to see how it's built |
| **D.7** | Compare PRAW to using `httpx` async — would async scraping be 10x faster? | When you're scraping 100+ subreddits and waiting hurts |
| **D.8** | Implement a SQLite layer with proper schema before idempotency atom | When the duplicate-tracking CSV gets unwieldy |
| **D.9** | Read Reddit's API ToS — what's allowed, what gets you banned | Before scaling up the scrape volume |
| **D.10** | Replace PRAW with `praw-async` or `asyncpraw` for concurrency | Performance optimization |

---

## Optional Sprint 5 — Feed into adlab (when adlab Layer 5+ is stable)

Once adlab has the dashboard + DB connection working, vox can write directly to a `voc_signals` table in the same Supabase. This is **future scope**, not in the initial deliverable.

- 5.1: Schema design for `voc_signals` table (posts + comments unified, with platform field)
- 5.2: Direct Postgres writes from Python (psycopg or supabase-py)
- 5.3: adlab dashboard page `/voc` that shows signals next to ads (cross-reference!)

---

## Total scope

| Phase | Atoms | Time | Client value |
|---|---|---|---|
| Sprint 0 | 1 | ~2 hr | CSV in their inbox |
| Sprint 1 | 5 | ~3-4 hr | Useful research tool |
| Sprint 2 | 5 | ~3 hr | Reliable for daily use |
| Sprint 3 | 3 | ~2 hr | Hands-off automation |
| Sprint 4 | 5 | ~5-6 hr | TikTok added |
| Deepening | 10 (optional) | varies | Wire-level understanding |
| **Core (S0-S3)** | **14** | **~10-11 hrs** | **Production-ready Reddit tool** |
| **+ TikTok** | 19 | ~16-17 hrs | **Multi-platform** |

**You could ship Sprint 0 tonight and Sprint 1 within the week.** That's the entire point.

---

## Today's first session (~90 min)

Atom 0.1 — the whole thing. End state: CSV file sent to client, GitHub repo with the script, working venv.

---

## 🔗 The Loom: Related Connections
- [[~/Projects/dev-learning/projects/adlab/CURRICULUM.md]] — sibling project; vox may eventually feed adlab's Supabase
- [[~/Projects/dev-learning/_framework/agents/architect.md]] — Architect agent prompt
- [[~/Projects/dev-learning/_framework/agents/mechanic.md]] — Mechanic agent prompt (use this one heavily for Sprint 0)
- [[~/Vaults/CROMaxLabs/thermoslim-platform/ad-intelligence/]] — long-term integration target
