# Runtime Profiles — Agent-Agnostic AI Routing

The Architect and Mechanic roles are **portable system prompts** — see `agents/architect.md` and `agents/mechanic.md`. You can run either role through any AI tool that accepts a system prompt.

This file documents the supported AI runtimes and the four shipped profiles.

---

## Supported AI runtimes

| Runtime | Binary / Command | How role prompts load | Strength | Cost (est.) |
|---|---|---|---|---|
| **Claude Code** | `claude` | Add prompt to `CLAUDE.md` or paste at start | Deep reasoning, tool use, MCPs | $$ per session |
| **Antigravity CLI** | `antigravity` | Add prompt to `.antigravitycli/system.md` | Gemini-powered, fast, lightweight | $-$$ |
| **Aider (any model)** | `aider --model <id> --read agents/<role>.md` | `--read` flag loads the prompt as context | Works with any OpenAI-compatible API including OpenRouter | Varies by model |
| **Aider + Kimi K2** | `aider --model openrouter/moonshotai/kimi-k2 --read agents/<role>.md` | Same | Excellent code, ~$0.30/M in, $2.50/M out | $ |
| **Ollama local** | `ollama run qwen2.5-coder` | Paste prompt in first message | Runs on Mac Studio, no network | $0 |
| **LM Studio (MLX)** | OpenAI-compatible API on local port | Aider points at `http://<tailscale-ip>:1234` | Use any MLX model on Mac Studio | $0 |
| **Cursor inline** | (built into Cursor editor) | Paste prompt into chat | Best when editing code directly | included in Cursor sub |
| **ChatGPT web** | (browser) | Paste prompt into Custom Instructions | Last-resort fallback | included in ChatGPT sub |

---

## Shipped profiles

Four ide.yml variants in `profiles/`. Switch by symlinking `ide.yml` → `_framework/profiles/<name>.yml`.

### `local.yml` (recommended default given your local LLM stack)

| Agent | Runtime | Model |
|---|---|---|
| Architect | LM Studio via Tailscale | Any reasoning model on Mac Studio |
| Mechanic | Ollama (local Mac) | qwen2.5-coder |

**Cost:** $0. **Tradeoff:** quality depends on local hardware; Architect explanations may be shorter or less polished than cloud.

### `cheap.yml`

| Agent | Runtime | Model |
|---|---|---|
| Architect | Aider + OpenRouter | Kimi K2 |
| Mechanic | Ollama (local Mac) | qwen2.5-coder |

**Cost:** ~$0.20/day. **Tradeoff:** cloud dependency for Architect, but Kimi K2 reasoning is solid.

### `balanced.yml`

| Agent | Runtime | Model |
|---|---|---|
| Architect | Claude Code | Sonnet |
| Mechanic | Aider + OpenRouter | Kimi K2 |

**Cost:** ~$1-2/day. **Tradeoff:** good quality both sides, modest spend.

### `premium.yml`

| Agent | Runtime | Model |
|---|---|---|
| Architect | Claude Code | Opus |
| Mechanic | Claude Code | Sonnet |

**Cost:** ~$5-10/day. **Use when:** real interview prep, deep concept sessions, debugging hard problems.

---

## Switching profiles

```bash
cd ~/Projects/dev-learning/projects/webhook-dashboard

# pick a profile
ln -sf ../../_framework/profiles/balanced.yml ide.yml

# restart tmux-ide to apply
tmux-ide restart
```

You can switch mid-session as needed.

---

## Loading agent prompts into different runtimes

**Claude Code:**
```bash
# Either put the contents of agents/architect.md into your CLAUDE.md
# OR paste it as your first message in a fresh session
claude
> /paste-from-clipboard  # after copying agents/architect.md
```

**Antigravity CLI:**
```bash
antigravity --system-prompt-file ../../_framework/agents/architect.md
# (verify exact flag against `antigravity --help` once installed)
```

**Aider:**
```bash
aider --model openrouter/moonshotai/kimi-k2 \
      --read ../../_framework/agents/architect.md \
      --no-auto-commits
```

**Ollama:**
```bash
ollama run qwen2.5-coder
# Then paste the contents of agents/mechanic.md as your first message.
# Subsequent messages stay in role.
```

**Cursor inline:**
Paste the prompt into the chat sidebar's "system" or "context" field once per session.

---

## When to switch profiles

| Situation | Profile |
|---|---|
| Daily learning, no interview pressure | `local.yml` or `cheap.yml` |
| Working through a tricky concept | `balanced.yml` (Claude on Architect) |
| Active interview prep, mock answers | `premium.yml` |
| Network down, offline | `local.yml` |
| Trying a brand-new layer for the first time | `balanced.yml` or `premium.yml` |
