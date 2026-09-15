# Implementation Plan: Database Setup (`database/db.py`)

## Context

`database/db.py` is currently a stub (5 lines of comments) per the training scaffold's "Step 1 — Database Setup". The spec at `.claude/specs/database-setup.md` defines the two-table schema (`users`, `expenses`) and the three functions (`get_db`, `init_db`, `seed_db`) that this app needs before any real feature (auth, expense CRUD) can be built — every later step depends on this data layer existing and working correctly. This plan implements that spec exactly and wires it into `app.py`'s startup.

Decisions confirmed with the user:
- DB file: **`expense_tracker.db`** in the project root (already covered by the existing `.gitignore` entry — no `.gitignore` change needed).
- FK delete behavior: **`ON DELETE CASCADE`** on `expenses.user_id → users.id` (no user-delete feature exists yet, but this avoids future orphaned rows).

## Files Changed

### 1. `database/db.py` — full implementation

```python
import os
import sqlite3
from datetime import date
from werkzeug.security import generate_password_hash

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
DB_PATH = os.path.abspath(os.path.join(BASE_DIR, "..", "expense_tracker.db"))


def get_db():
    conn = sqlite3.connect(DB_PATH)
    conn.row_factory = sqlite3.Row
    conn.execute("PRAGMA foreign_keys = ON")
    return conn


def init_db():
    conn = get_db()
    try:
        conn.execute("""
            CREATE TABLE IF NOT EXISTS users (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                email TEXT UNIQUE NOT NULL,
                password_hash TEXT NOT NULL,
                created_at TEXT NOT NULL DEFAULT (datetime('now'))
            )
        """)
        conn.execute("""
            CREATE TABLE IF NOT EXISTS expenses (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                user_id INTEGER NOT NULL,
                amount REAL NOT NULL,
                category TEXT NOT NULL,
                date TEXT NOT NULL,
                description TEXT,
                created_at TEXT NOT NULL DEFAULT (datetime('now')),
                FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
            )
        """)
        conn.commit()
    finally:
        conn.close()


def seed_db():
    conn = get_db()
    try:
        count = conn.execute("SELECT COUNT(*) FROM users").fetchone()[0]
        if count > 0:
            return

        password_hash = generate_password_hash("demo123")
        cursor = conn.execute(
            "INSERT INTO users (name, email, password_hash) VALUES (?, ?, ?)",
            ("Demo User", "demo@spendly.com", password_hash),
        )
        user_id = cursor.lastrowid

        today = date.today()
        expenses = [
            (12.50, "Food", 1, "Lunch at cafe"),
            (45.00, "Transport", 3, "Monthly metro pass top-up"),
            (150.00, "Bills", 5, "Electricity bill"),
            (60.00, "Health", 8, "Pharmacy purchase"),
            (25.00, "Entertainment", 11, "Movie tickets"),
            (80.00, "Shopping", 14, "New shoes"),
            (9.99, "Other", 17, "Miscellaneous"),
            (33.75, "Food", 20, "Grocery run"),
        ]

        for amount, category, day_offset, description in expenses:
            expense_date = today.replace(day=min(day_offset, 28)).strftime("%Y-%m-%d")
            conn.execute(
                """
                INSERT INTO expenses (user_id, amount, category, date, description)
                VALUES (?, ?, ?, ?, ?)
                """,
                (user_id, amount, category, expense_date, description),
            )

        conn.commit()
    finally:
        conn.close()
```

Key points:
- `DB_PATH` is computed from `__file__`, not CWD, so `expense_tracker.db` always lands in the project root regardless of where `python app.py` is invoked from.
- `PRAGMA foreign_keys = ON` is per-connection in SQLite (doesn't persist in the file) — set on every `get_db()` call, which is also what makes `ON DELETE CASCADE` actually take effect.
- `init_db()` uses `CREATE TABLE IF NOT EXISTS` for both tables — idempotent by construction, no Python-side existence check needed.
- `seed_db()` idempotency: `SELECT COUNT(*) FROM users`, return early if non-zero — prevents duplicate seed data on repeated `app.py` runs (including Werkzeug's debug-mode reloader, which imports the module twice).
- All 7 fixed categories (Food, Transport, Bills, Health, Entertainment, Shopping, Other) appear at least once across the 8 expenses; dates spread across the current month using `date.today().replace(day=...)`, clamped to `min(day, 28)` to stay valid in short months.
- All queries use `?` placeholders — no string formatting into SQL anywhere.
- No Flask `g`/teardown pattern exists yet in this app, so each function opens its own connection via `get_db()` and closes it in a `finally` block — self-contained and safe to call from anywhere (startup code today, route handlers in future steps).

### 2. `app.py` — import and startup wiring

```python
from flask import Flask, render_template
from database.db import get_db, init_db, seed_db

app = Flask(__name__)

# ... existing routes unchanged ...

# ------------------------------------------------------------------ #
# Database startup                                                    #
# ------------------------------------------------------------------ #

with app.app_context():
    init_db()
    seed_db()


if __name__ == "__main__":
    app.run(debug=True, port=5001)
```

- Placed at module level (outside `if __name__ == "__main__":`) so the DB is also initialized/seeded if the app is ever imported by a WSGI runner or test fixture, not just via `python app.py`.
- `get_db` is imported now even though no route calls it yet, per the spec — future steps (login/register/expense CRUD) will use it directly.

### 3. `.claude/rules/specs-location.md` (new) and `CLAUDE.md`

Established the project convention that specs live in `.claude/specs/`, implementation plans in `.claude/plans/`, and rules in `.claude/rules/`, with a short pointer bullet added to `CLAUDE.md`.

## Files NOT Changed

- `database/__init__.py` — stays empty (package marker only).
- `.gitignore` — already ignores `expense_tracker.db` literally; no change needed since that's the filename being used.
- `requirements.txt` — no new dependencies; `werkzeug==3.1.6` already provides `werkzeug.security`.

## Verification (no automated tests exist yet — manual end-to-end check)

1. Run `python app.py`, confirm no traceback, confirm `expense_tracker.db` appears in the project root.
2. Inspect data:
   ```
   python -c "from database.db import get_db; c = get_db(); print(c.execute('SELECT * FROM users').fetchall()); print(c.execute('SELECT category, COUNT(*) FROM expenses GROUP BY category').fetchall()); print(c.execute('SELECT COUNT(*) FROM expenses').fetchone()[0]); c.close()"
   ```
   Expect: 1 user row, 8 expense rows total, all 7 categories represented, dates in `YYYY-MM-DD` within the current month.
3. Confirm UNIQUE email constraint: attempt a second insert with `email="demo@spendly.com"` via `get_db()` — expect `sqlite3.IntegrityError`.
4. Confirm FK enforcement: attempt an expense insert with a bogus `user_id` (e.g. `9999`) — expect `sqlite3.IntegrityError`.
5. Confirm idempotency: re-run `python app.py` (or call `seed_db()` twice in one session) — re-run the count query, still exactly 1 user and 8 expenses.
6. Optional: `sqlite3 expense_tracker.db ".schema"` to visually confirm both `CREATE TABLE` statements match the spec's column names/types/constraints exactly, and `.tables` to confirm only `users` and `expenses` exist.
