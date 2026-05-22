# Webhook Dashboard

A webhook integration dashboard built as part of the dev-learning curriculum.

**What it does:** receives events from an external service, stores them in a PostgreSQL database, exposes a REST API to query them, and displays them in a minimal frontend dashboard. Layer 7 adds a Claude API endpoint that answers questions about the data.

**Why it exists:** this project touches every layer of full-stack development — HTTP, SQL, backend, frontend, APIs, deployment, and AI integration. Every atom in the curriculum adds one piece. The commit history shows the build layer by layer.

## Stack

- **Backend:** Node.js + Express
- **Database:** PostgreSQL (local) via TablePlus
- **Frontend:** Plain HTML + JavaScript (no framework — intentional)
- **AI:** Claude API (Layer 7)
- **Deploy:** Vercel or Railway (Layer 6)

## Progress

See `_progress.md` for current status and next atom.

## Running locally

_(filled in during Layer 3 — backend setup)_
