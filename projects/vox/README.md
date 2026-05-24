# vox

Voice-of-Customer scraping tool. **Ship-first** Reddit (later TikTok) scraper for a real client's research workflow. Outputs CSV/JSON. Learning happens alongside delivery, not before.

## What it does

Pulls posts + comments from configured subreddits/keywords. Writes structured CSV (default) or JSON. Daily/manual cron. Tracks what it's already pulled (idempotent).

## Stack

- **Python 3.12** in a venv
- **PRAW** (Python Reddit API Wrapper) — handles OAuth, pagination, rate limits
- **pandas** — CSV writing + light analysis
- **APScheduler** — cron-style scheduling (later)
- **Playwright (Python)** — TikTok scraping (Sprint 5)

## Mode of operation: Sprints, not Layers

This project uses **sprints** instead of layers. Each sprint is a shippable increment with client value. Deep wire-level learning is *lateral* — done as Deepening atoms when you hit something interesting, not pre-emptively.

See [CURRICULUM.md](./CURRICULUM.md) for the sprint plan.

## Where to start

1. Read `CURRICULUM.md` → Sprint 0
2. Sprint 0 ends with a CSV you can email to the client. ~90-120 min total.
3. Update `_progress.md` after each sprint atom

## Relationship to adlab

Sibling project. Reuses the same `_framework/` (agents, atom format, vocabulary). Eventually may feed scraped data into the adlab Supabase as a Voice-of-Customer signal — not in initial scope.
