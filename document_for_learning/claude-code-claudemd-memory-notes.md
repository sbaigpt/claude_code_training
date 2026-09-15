# Claude Code — CLAUDE.md, .claude Folder & Auto Memory (Revision Notes)

> How Claude Code "remembers" your project across sessions. Theoretical but foundational — used in every later topic.

---

## 1. Why CLAUDE.md Exists

LLMs have **no memory** — and Claude Code runs on an LLM, so it **forgets past sessions**.
Without a fix, every new session means re-explaining your whole project from scratch (DB setup, libraries, coding conventions…).
That's **cumbersome** *and* **error-prone** (you'll forget things) → inconsistent code, painful on big projects.

**Solution:** put all project details in one file that Claude auto-reads at the start of every session.

---

## 2. What CLAUDE.md Is

A **project-level instruction file** that guides how Claude Code behaves on your codebase.
→ Think of it as a **persistent system prompt**. It's just a **markdown file**, auto-loaded at the start of **every new session**, living in your **project directory**.

### How to create it (two ways)
| Way | How | Notes |
|---|---|---|
| **Manual** | Create a file named **`CLAUDE.md`** (capitals) in project root, write details | Good for fine-grained control |
| **`/init`** | Claude scans & analyzes your whole codebase and auto-generates it | Preferred — faster, great for unfamiliar/large codebases |

**Most people:** generate with `/init`, then edit.

### How `/init` works
Spins up an internal agent that scans **high-signal files first** (`package.json`, `requirements`, `README`), maps the **directory tree**, notes **tech stack / folder layout / naming conventions**, then writes `CLAUDE.md` to the project root.
⚠️ The generated file is only **~30% useful** — you must add the other **70%** yourself (workflows, constraints, what to avoid, deployment, conventions). **It's a starting point, not the end point.**

---

## 3. What a Good CLAUDE.md Contains

1. **Project overview** — one-line description (*e.g. "FastAPI backend for a health-tracking app that stores patient records and exposes CRUD APIs"*).
2. **Architecture** — what lives where (*e.g. routes in `routes/`, business logic in `services/`, schemas in `schemas/`*).
3. **Coding style** — conventions (*type hints, Pydantic models, small focused functions*).
4. **Preferred libraries & tools** — what to use and not go beyond (*FastAPI, Pydantic, SQLAlchemy*).
5. **Commands** — exact run/test/deploy commands (install deps, start dev server, run tests).
6. **Critical rules** — warnings, edge cases, things to avoid (*"don't touch `database.py` unless needed", "don't generate patient IDs yourself"*).

💡 A **development roadmap** (feature order + a status column) helps Claude behave consistently across sessions and streamlines the whole workflow.
*(This is one guideline, not the only one — study others' styles and build your own.)*

---

## 4. The `.claude` Folder

A **local configuration directory** controlling how Claude Code behaves — per project or across all projects.
Stores config for **skills, custom slash commands, sub-agents**, etc. → Claude Code's **toolbox**.

### Two types
| | Project-level `.claude` | Global / User-level `.claude` |
|---|---|---|
| **Location** | Project root | Home directory (`~/.claude`) |
| **Scope** | One project | Every project on your machine |
| **Shared with team?** | Yes — lives in the repo (git) | No — stays on your machine |
| **Use for** | Project-specific commands/workflows/settings | Personal stuff you want everywhere (your coding style, preferred tools) |

### What's inside
- **`settings.local.json`** — tool permissions (personal, project-level, not shared)
- **`commands/`** — your custom slash commands (e.g. a code-review command)
- **`rules/`** — split-out CLAUDE.md content *(see §6)*
- **`skills/`** — markdown files on how to do a task type; auto-load only when needed
- **`agents/`** — your sub-agents (e.g. a code reviewer)
- *(User-level also holds a per-project directory + your stored sessions)*

---

## 5. The 5 Types of CLAUDE.md (by location)

1. **Project root `CLAUDE.md`** — auto-loaded every session (the standard one).
2. **Inside project `.claude/` folder** — *no difference* from root; auto-loaded. For people who like all config in one place.
3. **`CLAUDE.local.md`** — read alongside the main file, **auto git-ignored** → personal project-level tricks that never hit the repo.
4. **Home `~/.claude/CLAUDE.md`** (user level) — personal preferences across **all** projects (style defaults, preferred tools, working style).
5. **`CLAUDE.md` inside a subfolder** — for large repos. **NOT auto-loaded** every session; loads only when Claude works in that folder and needs its context. (Claude recurses up to the root reading any `CLAUDE.md` / `CLAUDE.local.md` it finds.)

---

## 6. Keeping CLAUDE.md Healthy (Best Practices)

- **Start with `/init`, then trim** what's unnecessary — better than writing from scratch.
- **Commit** every change to CLAUDE.md via git.
- **Only universal things** — not feature- or section-specific details.
- **Use `IMPORTANT` sparingly.** *"If everything is important, nothing is."*
- **Keep it under ~200 lines** (200–300 max). More instructions → worse instruction-following (true for all LLMs).
  - **Rule of thumb:** *"If I delete this line, will Claude start making mistakes?"* If **no** → delete it.
- **Living document** — refresh after each feature (add new, remove stale), grow it organically, commit as you go.
- **Codify repeated mistakes** — when Claude keeps making the same error, fix it *and* tell it to add the rule to CLAUDE.md.
- **Audit periodically** (weekly/monthly) for instruction drift — old rules can become redundant or counterproductive.

### If it exceeds 200 lines → 3 fixes
1. **Split into `.claude/rules/`** as topic files (`code-style.md`, `testing.md`, `security.md`…). Benefits: **lazy loading** (loaded only when needed) + **maintainability**.
2. **Use imports** inside CLAUDE.md — like Python imports, pull a topic from another file.
3. **Subdirectory `CLAUDE.md` files** — for big projects (front-end / back-end / database each get their own), loaded when Claude works there.

---

## 7. Auto Memory

A **persistent directory where Claude records learnings, patterns & insights as it works.**
While you build, Claude silently observes; when it spots something meaningful, it saves it to a file called **`memory.md`**.

- *Example:* your project tracks expenses in **INR not USD** → Claude notices the pattern and stores it in `memory.md`.
- **Next session:** both `CLAUDE.md` **and** `memory.md` load automatically.
- **Location:** `~/.claude/projects/<your-project>/memory/memory.md`
- Only the **top ~200 lines** of `memory.md` load each session → keep it managed.

### Access with `/memory`
View **and** edit. Three options:
- **Project memory** → opens the project's `CLAUDE.md`
- **User memory** → opens the home-directory `CLAUDE.md`
- **Open auto-memory folder** → opens `memory.md`

### You can add memories too
Not just Claude. End of a session, prompt: *"Based on what we discussed and built this session, update your memory.md."* → Claude writes it. Mostly it does this on its own, but the option is yours.

---

## 8. The Key Link: CLAUDE.md ↔ memory.md

Both are **persistent memory**, both **auto-load every session**. The only real difference:

| File | Written by |
|---|---|
| `CLAUDE.md` | **You** (the programmer) |
| `memory.md` | **Claude** (automatically) |

Otherwise they do the same job — that's why `/memory` shows both.

---

## One-line takeaway
`CLAUDE.md` is your persistent system prompt for the project (you write it); `memory.md` is Claude's auto-learned notes (it writes it) — both reload every session. Generate with `/init`, keep it lean (<200 lines), and treat it as a living document.
