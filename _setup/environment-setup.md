# Environment Setup

One-time setup. Do this before your first atom. ~45 minutes total.

## 1. Cursor

Download from cursor.com. Install. Open it — it looks like VS Code because it IS VS Code under the hood.

**Verify:** open Cursor, open the terminal inside it (Ctrl+` or View → Terminal). You should see a shell prompt.

## 2. Node.js

Required to run JavaScript outside the browser.

```bash
# Install via homebrew (Mac)
brew install node

# Verify
node --version   # should print v20+
npm --version    # should print 10+
```

## 3. PostgreSQL (local)

Local database for learning — no internet required, instant feedback.

```bash
brew install postgresql@16
brew services start postgresql@16

# Verify
psql --version
```

## 4. TablePlus

Download from tableplus.com. Free tier is enough.

Connect to local Postgres:
- Host: localhost
- Port: 5432
- User: your Mac username
- Password: (leave blank for local)
- Database: postgres

**Verify:** you can connect and see the default tables.

**Optional — connect to Supabase:**
Go to your Supabase project → Settings → Database → Connection String. Paste into TablePlus as a new connection. You can now browse your real project data alongside the local learning database.

## 5. GitHub

If you don't have an account: github.com → Sign up.

Install git if not already installed:
```bash
brew install git
git --version
```

Configure your identity:
```bash
git config --global user.name "theycallmeigor"
git config --global user.email "igor@idvin.co"
```

**Verify:** run `git status` in any folder — you should see output (even if it says "not a git repository").

## 6. Create the webhook-dashboard repo

```bash
cd ~/Projects/dev-learning/projects/webhook-dashboard
git init
git add README.md
git commit -m "init: project scaffold"
```

Then create a matching repo on GitHub (github.com → New repository → name: webhook-dashboard) and push:

```bash
git remote add origin https://github.com/theycallmeigor/webhook-dashboard.git
git branch -M main
git push -u origin main
```

**Verify:** open github.com/theycallmeigor/webhook-dashboard — you should see your README.

---

Setup complete. You're ready for Atom 1.1.
