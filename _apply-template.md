# How to Start a New Learning Project

Use this when you want to apply the dev-learning framework to a new topic (e.g., deep SQL, Python, data pipelines).

## Steps

1. **Create the project folder**
   ```bash
   mkdir -p ~/Projects/dev-learning/projects/<your-project-name>/{_atoms,src}
   ```

2. **Copy the essentials**
   ```bash
   cp _framework/atom-format.md projects/<your-project-name>/atom-format.md
   cp _framework/interview-survival.md projects/<your-project-name>/interview-survival.md
   ```

3. **Create a README and _progress.md**
   - README: what is this project, what does it build, what stack
   - _progress.md: current layer, next atom, interview readiness table

4. **Init a git repo and push to GitHub**
   ```bash
   cd projects/<your-project-name>
   git init
   git add .
   git commit -m "init: project scaffold"
   # create repo on github, then:
   git remote add origin https://github.com/theycallmeigor/<project-name>.git
   git push -u origin main
   ```

5. **Start with Atom 0 — Environment Setup**
   If the new project needs different tools, document them in a `_setup.md` in the project folder.

6. **Write your first atom**
   Use `atom-format.md` as the template. Save it to `_atoms/0-1-setup.md`.

## Shared resources

The `_framework/vocabulary.md` is shared across all projects — add terms there, not in individual project folders.
