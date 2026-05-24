# How to Start a New Learning Project

Use this when you want to apply the dev-learning framework to a new topic (e.g., deep SQL, Python, data pipelines, a new MasterApp feature).

## Steps

1. **Create the project folder structure**
   ```bash
   PROJECT=<your-project-name>
   cd ~/Projects/dev-learning
   mkdir -p projects/$PROJECT/{_atoms,src,logs/webhooks}
   touch projects/$PROJECT/logs/.gitkeep
   touch projects/$PROJECT/logs/webhooks/.gitkeep
   ```

2. **Copy the per-project workspace files**
   ```bash
   for f in _bug-log.md _questions.md _decisions.md _architecture.md _reading-queue.md _progress.md; do
     cp projects/webhook-dashboard/$f projects/$PROJECT/$f
   done
   ```
   Then open each and clear out webhook-dashboard-specific content.

3. **Copy and adapt the tmux config**
   ```bash
   cp _framework/ide-template.yml projects/$PROJECT/ide.yml
   ```
   Edit `ide.yml`: set `name:` to your project name, customize pane commands for the stack (e.g., swap `psql` for `redis-cli` if not using Postgres).

4. **Copy the .gitignore**
   ```bash
   cp projects/webhook-dashboard/.gitignore projects/$PROJECT/.gitignore
   ```

5. **Write a README.md**
   What is this project, what does it build, what stack, why this stack (the why goes into `_decisions.md` too).

6. **Init git and push to GitHub**
   ```bash
   cd projects/$PROJECT
   git init
   git add .
   git commit -m "init: project scaffold"
   # create repo on github.com, then:
   git remote add origin https://github.com/theycallmeigor/$PROJECT.git
   git branch -M main
   git push -u origin main
   ```

7. **Launch the tmux IDE**
   ```bash
   cd ~/Projects/dev-learning/projects/$PROJECT
   tmux-ide validate --json
   tmux-ide
   ```

8. **Write your first atom**
   Use `_framework/atom-format.md` as the template. Save to `_atoms/0-1-setup.md`.

## Shared resources (don't copy — reference)

These live in `_framework/` and are shared across all projects:

- `vocabulary.md` — add terms here, not per-project
- `learning-philosophy.md` — the B+C hybrid method
- `atom-format.md` — atom template
- `interview-survival.md` — MVA answers for each layer
- `ide-template.yml` — the tmux config template
