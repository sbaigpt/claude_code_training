# Claude Code — Slash Commands (Revision Notes)

> Quick-revise sheet. Type `/` anytime to see the full command list with descriptions.

---

## 1. What are Slash Commands?

Shortcuts you type inside a Claude Code session, starting with `/`, that **instantly trigger a predefined action/workflow** — no need to write out a full prompt.

**Why they exist:** developers keep repeating the same workflows. Instead of re-typing prompts, one command runs the whole reusable pattern.

**Two types:**
- **Built-in** → ship with Claude Code by default.
- **Custom** → you create them for your own repeated workflows.

---

## 2. Sessions (understand this first)

A **session = one conversation** with Claude Code.

- Starts when you run `claude`; ends with `/exit`.
- Each has a **unique ID** and stores the **full message history**.
- Saved **automatically** → can be **resumed anytime**, even after closing the terminal.
- Resume from terminal: **`claude -r`** → shows past conversations, pick one with arrow keys.

**Good habits:**
- **One task/feature per session** → keeps context clean, avoids mixing.
- **Rename a session immediately** → don't let AI auto-name it from your first question.
- **Commit frequently** at every milestone.
- **Export important sessions** before a big refactor.

---

## 3. Command Cheat-Sheet

**Session management**
| Command | What it does |
|---|---|
| `/exit` | Close the session |
| `/resume` | Switch to another session mid-way |
| `/rename <name>` | Rename the current session |
| `/export <file.md>` | Save the whole conversation as a file in the project dir |
| `/btw <question>` | Ask a **side question without polluting context**; press **Space** to dismiss the Q&A |

**Account**
| Command | What it does |
|---|---|
| `/login` | Log in |
| `/logout` | Log out (switch between personal / company accounts) |

**Models & usage**
| Command | What it does |
|---|---|
| `/model` | Switch between Opus / Sonnet / Haiku |
| `/usage` | Check current session % used + weekly limit |
| *extra usage* | Mid-way **top-up** ($5/$10…) when limit is hit — no waiting for reset |
| `/stats` | Usage statistics (tokens, models, sessions, streak) |
| `/insights` | Detailed **HTML report** on how you use Claude Code + how to improve |

**Settings**
| Command | What it does |
|---|---|
| `/config` | Change settings (thinking mode, verbose, progress bar, language) |
| `/permissions` | Allow / deny tools |
| `/theme` | Change theme (dark / light, etc.) |
| `/voice` | Voice mode — hold **Space** to speak instead of typing |

---

## 4. Models — Which to Pick

| Model | Nature | Use for |
|---|---|---|
| **Opus** | Most powerful, most expensive (eats tokens fast) | Complex tasks & planning |
| **Sonnet (4.6)** | Default, best balance of speed/quality/cost | Everyday coding *(used most)* |
| **Haiku** | Fastest, cheapest | Simple, repetitive tasks |

**Power pattern:** Use **Opus to plan** (architecture, specs, decisions) → switch to **Sonnet to implement** (reliable code generation).

---

## 5. Permissions (quick concept)

Claude Code = an **agent built on an LLM**. Its real power = **tools**:
- **Read** (read your files) · **Write** (write code) · **Bash** (run commands) · **Web Search** (fetch docs) · **+ custom tools via MCP**

By default Claude Code **asks permission** before using a tool. `/permissions` lets you set this once so it stops asking repeatedly.

- **Tabs:** Allow · Ask · Deny · Workspace
- **Add a rule**, e.g. `Bash(git init)` or `WebSearch`
- **Save scope:** Local project (just you) · Global project (shared via repo) · User (all your projects)
- **Stored in** `.claude/settings.local.json`
- ⚠️ **Be careful** — never auto-allow a command you wouldn't want run without asking.

---

## One-line takeaway
Slash commands turn repeated prompts into single keystrokes. Learn each one **as you need it** — no need to memorize all upfront.
