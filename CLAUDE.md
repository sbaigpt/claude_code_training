# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Spendly** — a Flask expense-tracker web app, built as a step-by-step training scaffold (this repo comes from a Claude Code / CampusX course). Route handlers and `database/db.py` contain comments like "coming in Step N" marking work that hasn't been implemented yet. See `summary.md` for a fuller snapshot of current scaffold state.

## Commands

```bash
# Setup (Windows venv already exists at .venv/)
pip install -r requirements.txt

# Run the dev server (http://localhost:5001)
python app.py

# Run tests (no tests/ directory exists yet — pytest-flask is installed for when it's added)
pytest
```

There is no build step, linter, or bundler configured — plain Python/Flask with no frontend framework.

## Architecture

- **`app.py`** — single-file Flask app; all routes are defined here directly (no blueprints). Currently: `/`, `/register`, `/login` render real templates; `/logout`, `/profile`, and the `/expenses/*` CRUD routes are unimplemented stubs returning placeholder strings.
- **`database/db.py`** — the intended data-access layer, currently just a stub comment describing the three functions students must implement: `get_db()` (SQLite connection with `row_factory` + foreign keys on), `init_db()` (create tables with `CREATE TABLE IF NOT EXISTS`), `seed_db()` (sample dev data). No ORM — raw `sqlite3`.
- **`templates/`** — Jinja2 templates extending `base.html` (nav/footer shell, "Spendly" branding). `landing.html`, `login.html`, `register.html` exist; templates for the expense CRUD flows and profile page do not yet exist and will need to be created alongside their routes.
- **`static/css/style.css`** / **`static/js/main.js`** — plain CSS/JS, no build tooling; `main.js` is currently an empty placeholder for future step work.
- **`document_for_learning/`** — the student's own notes on Claude Code features (slash commands, context window), not part of the app itself.

Since routes, templates, and the DB layer are being filled in incrementally per numbered step, when implementing a stubbed route check whether its template already exists and whether it depends on `database/db.py` functions that also still need to be written.

## Conventions

- Specs, technical design plans, and project rules live under `.claude/` — see `.claude/rules/specs-location.md`.
