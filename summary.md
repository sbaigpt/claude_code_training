# Project Summary

## Overview

This is an **expense tracker web app** built with Flask — it looks like a training/course scaffold (given the `claude_campusx` folder path and in-code comments like "Students will implement this in Step N"), likely from a Claude Code / CampusX exercise series.

**Purpose:** let users register, log in, and then track personal expenses — add, edit, delete, and view them.

### Current state of the scaffold

- Landing, login, and register pages are built and rendered (`templates/landing.html`, `login.html`, `register.html`)
- Auth routes (`/register`, `/login`) render templates but have no real logic yet
- `/logout`, `/profile`, and expense CRUD routes (`/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete`) are stubs returning placeholder text ("coming in Step 3/4/7/8/9")
- `database/db.py` is an empty stub documenting the expected functions (`get_db()`, `init_db()`, `seed_db()`) — no DB logic implemented yet

So it's essentially a skeleton app with the UI shell and route map in place, waiting for the database layer, auth, and expense CRUD to be built out step by step.

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python, Flask 3.1.3 (Werkzeug 3.1.6) |
| Database | SQLite via Python's built-in `sqlite3` (no ORM) |
| Templating | Jinja2 (Flask's default) |
| Frontend | Plain HTML/CSS/JS — `static/css/style.css`, `static/js/main.js`, no framework |
| Testing | pytest 8.3.5 + pytest-flask 1.3.0 |
| Dev server | Flask's built-in server (`debug=True`, port 5001) |

Minimal, no-build-tooling Python web stack — no ORM, no frontend framework, no bundler.
