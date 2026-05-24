# Living Vocabulary

Add terms here as you encounter them. One line per term. This grows — don't try to fill it upfront.

## Format

`**Term** — what it actually is (one sentence)`

---

## Web / HTTP

**HTTP** — the protocol browsers and servers use to talk to each other — a request goes out, a response comes back
**Request** — a message from a client (browser, terminal, app) asking a server to do something
**Response** — the server's reply — includes a status code and usually data
**Status code** — a number that tells you what happened: 200 = OK, 404 = not found, 500 = server error
**JSON** — a text format for sending structured data between a server and a client
**REST** — a convention for designing APIs using URLs as resources and HTTP methods as actions
**API** — a defined way for two programs to talk to each other
**Webhook** — an API in reverse — instead of you asking a server for data, the server pushes data to you when something happens
**Endpoint** — a specific URL on a server that accepts requests and does something

## SQL / Databases

**Database** — an organized store of data, structured into tables
**Table** — rows and columns, like a spreadsheet, but structured and queryable
**Row** — one record in a table
**Column** — one field across all records (e.g., "created_at", "event_type")
**Query** — a question you ask the database, written in SQL
**SELECT** — SQL command to read data from a table
**WHERE** — SQL clause to filter rows by a condition
**JOIN** — SQL clause to combine rows from two tables based on a related column
**Index** — a shortcut the database builds so it can find rows faster — like a book index
**Schema** — the structure of your database — which tables exist, which columns, what types
**Migration** — a versioned change to your schema — how you add or change tables safely
**Foreign key** — a column that references the ID of a row in another table — how tables relate

## Backend

**Server** — a program that listens for incoming requests and sends back responses
**Route** — a URL pattern mapped to a function that handles requests to that URL
**Handler** — the function that runs when a request hits a route
**Middleware** — code that runs on every request before it hits your handler (logging, auth checks, etc.)
**Node.js** — JavaScript runtime that lets you run JS outside the browser, on a server
**Express** — a Node.js framework that makes building HTTP servers easier

## Git / GitHub

**Repository (repo)** — a folder tracked by git — contains your code and its full history
**Commit** — a saved snapshot of your changes with a message describing what changed
**Branch** — a parallel version of your code — you work on it without affecting main
**Main** — the primary branch — the source of truth
**Pull request (PR)** — a proposal to merge your branch into main — reviewable before merging
**Merge** — combining one branch's changes into another
**Clone** — copying a remote repo to your local machine
**Push** — sending your local commits to the remote repo (GitHub)
**Pull** — fetching and applying remote changes to your local branch
**Diff** — a view of exactly what changed between two versions of a file
**Stash** — temporarily saving uncommitted changes so you can switch branches
**Rebase** — replaying your commits on top of another branch — cleaner history than merge
**Worktree** — having two branches checked out simultaneously in different folders

## Dev Practices

**Refactoring** — rewriting code to be cleaner without changing what it does
**Linting** — automated rules that flag bad code style before you run it (ESLint for JS)
**Formatting** — auto-fixing spacing/style consistently (Prettier)
**Dependencies** — libraries your project uses — managed via package.json in JS
**Environment variables** — config values stored outside your code (API keys, DB URLs) — lives in .env
**Build** — transforming source code into something a server or browser can run
**CI/CD** — automated tests and deployment that run every time you push to GitHub
**Staging vs Production** — two copies of your app — staging is where you test, prod is what users see
**Technical debt** — shortcuts taken now that make future changes harder
**DRY** — "Don't Repeat Yourself" — don't write the same logic twice
**Unit test** — testing one function in isolation
**Integration test** — testing how two or more parts work together
**Scaffolding** — auto-generating boilerplate to start a project

## CLI / Unix Conventions

**Dot-folder** — hidden folder in your home directory where a CLI tool stores its per-user config (`.claude/`, `.gemini/`, `.antigravitycli/`). 40+ year Unix convention. Spotting it tells you where any tool stores settings without reading docs.
**Binary** — the actual executable file you run from the terminal (`claude`, `node`, `git`). Lives somewhere in your `PATH`.
**PATH** — environment variable listing folders the shell searches when you type a command. `which <cmd>` shows you which file gets run.
**Symlink** — a "shortcut" file that points to another file. `ln -s target link` creates one. Used here to point `ide.yml` at a profile in `_framework/profiles/`.
**Environment variable** — a key-value pair living in your shell session — apps read these for config. Examples: `OPENROUTER_API_KEY`, `PATH`. Set via `export KEY=value`.

## AI / LLM

**LLM** — Large Language Model — the AI model itself (Claude, GPT-4, etc.)
**Prompt** — the input you send to an LLM
**Completion** — the output the LLM returns
**Tool use / function calling** — giving the LLM the ability to call your code — it decides when and what arguments to pass
**RAG** — Retrieval-Augmented Generation — fetch relevant data first, inject it into the prompt as context, then ask the LLM
**Streaming** — getting the LLM response token-by-token as it generates, instead of waiting for the full response
**Context window** — how much text an LLM can hold in memory at once — has a hard limit
**Embedding** — a numerical representation of text used for semantic search
**Agent** — an LLM in a loop — it takes actions, observes results, decides next steps, repeats
