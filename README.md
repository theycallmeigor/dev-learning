# dev-learning

A reusable template for structured, project-anchored learning. Built for 20-minute learning atoms — no momentum required, no scheduled sessions.

## How to use this template

1. Read `_framework/learning-philosophy.md` to understand the B+C hybrid approach
2. Follow `_setup/environment-setup.md` to get your tools ready
3. Open `projects/webhook-dashboard/` — that's the first project, start there
4. Each atom is documented in `projects/<name>/_atoms/` — one file per completed atom

## To start a new learning project

1. Create a new folder under `projects/`
2. Copy `_framework/atom-format.md` to get the atom template
3. Copy `_framework/interview-survival.md` and adapt it to the new topic
4. Build — commit every atom

## Structure

```
_framework/          ← reusable learning system (don't modify per project)
_setup/              ← one-time environment setup
projects/            ← one folder per learning project
  webhook-dashboard/ ← the anchor project (full-stack + GitHub + AI/LLM)
```
