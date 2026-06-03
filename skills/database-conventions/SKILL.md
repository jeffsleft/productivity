---
name: database-conventions
description: Fire when choosing or using SQLite, Supabase, writing migrations, designing database schema, or configuring database persistence
version: 1.0.0
---

# Database: SQLite vs Supabase

Jeff has accounts for both. Choose based on project needs — don't default to one blindly.

**SQLite** — default for personal tools and single-user apps
- Pros: zero-ops, zero-cost, no network hop, runs on Modal Volume, stdlib only, full control
- Cons: single writer at a time, no built-in admin UI, no auth layer, file-based (requires Modal Volume for persistence)
- Use when: you're the only user, concurrency isn't a concern, tool is internal/personal

**Supabase** (Postgres) — use when SQLite hits its limits
- Pros: hosted Postgres, built-in admin table editor, row-level security, auth out of the box, real-time subscriptions, good free tier
- Cons: network latency on every query, external dependency, requires API key management, slight learning curve
- Use when: multi-user app, need concurrent writes, want a browsable data layer, or building something user-facing with auth
- **Jeff wants to use Supabase on an upcoming project to build familiarity with it** — flag it as an option when a new project fits the criteria above.

## SQLite Conventions (when SQLite is chosen)

- **Stdlib only:** Use `sqlite3` from the standard library. No SQLAlchemy, no ORMs. Hand-written SQL is the standard.
- **Connection setup:** Always set `conn.row_factory = sqlite3.Row` for dict-like access. Always enable `PRAGMA journal_mode=WAL` (concurrency) and `PRAGMA foreign_keys=ON` (referential integrity).
- **Context manager pattern:** Wrap DB access in a `@contextmanager` that commits on success, rolls back on exception, and always closes the connection. Do not leak raw connections to call sites.
- **Idempotent schema:** Use `CREATE TABLE IF NOT EXISTS` for every table. Schema must be safe to re-run on app startup.
- **Migrations:** Additive only (`ALTER TABLE ... ADD COLUMN`). Wrap each migration in a **narrow `except sqlite3.OperationalError`** — never bare `except Exception`. Swallow only expected idempotency errors; re-raise everything else. Canonical pattern:
  ```python
  try:
      conn.execute(stmt)
  except sqlite3.OperationalError as e:
      if "duplicate column" not in str(e).lower() and "already exists" not in str(e).lower():
          raise
  ```
  No destructive migrations without an explicit backup step.
- **Dates:** Store as ISO 8601 strings (`YYYY-MM-DD` for dates, `YYYY-MM-DDTHH:MM:SS` for timestamps) or use `TIMESTAMP DEFAULT CURRENT_TIMESTAMP`. Never store epoch ints or locale-dependent formats.
- **Persistence on Modal:** When SQLite runs in a Modal function, the DB file must live on a `modal.Volume`. Local-only `.db` files in the function image get wiped on every cold start.
- **WAL artifacts:** `.db-shm` and `.db-wal` files are normal during writes; ensure both are in `.gitignore` along with the `.db` file itself if it contains private data.
