# Architecture — vox

## Sprint 0 (minimum viable)

```
   ┌──────────────────────┐
   │   .env (gitignored)  │
   │   REDDIT_CLIENT_ID   │
   │   REDDIT_SECRET      │
   └──────────┬───────────┘
              │ python-dotenv loads
              ▼
   ┌──────────────────────┐         ┌────────────────┐
   │  src/scrape.py       │ ──PRAW─▶│  Reddit API    │
   │  (Python script)     │ ◀────── │  (OAuth + REST)│
   └──────────┬───────────┘         └────────────────┘
              │ pandas.to_csv()
              ▼
   ┌──────────────────────┐
   │  data/skincare_      │
   │  2026-05-24.csv      │
   └──────────────────────┘
```

## Target state (end of Sprint 3)

```
   ┌──────────────────────┐
   │   launchd / cron     │  6am daily
   │   ↓                  │
   └──────────┬───────────┘
              │
              ▼
   ┌──────────────────────────────────┐
   │  src/vox.py (CLI tool)           │
   │                                  │
   │  config.yml → which subreddits   │
   │              + keywords          │
   │                                  │
   │  ┌────────────────────────────┐  │
   │  │  reddit_client.py (PRAW)   │  │──▶ Reddit API
   │  └────────────────────────────┘  │
   │  ┌────────────────────────────┐  │
   │  │  dedup.py (SQLite track)   │  │──▶ data/seen.db
   │  └────────────────────────────┘  │
   │  ┌────────────────────────────┐  │
   │  │  writer.py (CSV + Drive)   │  │──▶ data/*.csv
   │  └────────────────────────────┘  │
   │  ┌────────────────────────────┐  │      ┌─────────────┐
   │  │  notifier.py (Slack/email) │  │─────▶│ Google Drive│
   │  └────────────────────────────┘  │      └─────────────┘
   └──────────────────────────────────┘
                  │ on failure
                  ▼
              Slack webhook
```

## Sprint 4+ (TikTok added)

```
   ┌─────────────┐    ┌─────────────────┐
   │  Reddit     │    │  TikTok         │
   │  (PRAW)     │    │  (Playwright)   │
   └──────┬──────┘    └────────┬────────┘
          │                    │
          ▼                    ▼
   ┌──────────────────────────────────┐
   │  Unified writer.py               │
   │  one schema, platform tag        │
   └─────────────┬────────────────────┘
                 ▼
        data/voc_2026-05-24.csv
```

## Sprint update log
- **Sprint 0 state:** _(not built)_
- **Sprint 1 state:** _(not built)_
- ...
