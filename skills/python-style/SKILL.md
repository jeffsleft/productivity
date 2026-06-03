---
name: python-style
description: Fire when writing Python code, reviewing Python style, working with f-strings, Jinja2 templates, YAML config loading, or setting Python version
version: 1.0.0
---

# Python Style

- **Version:** Python 3.12+ unless a project has a stated reason otherwise.
- **Style:** Functional-first. Type hints required for public functions. PEP 8.
- **Dependencies:** Stdlib-first. Minimal third-party deps. Justify each non-obvious new dep in the project `CLAUDE.md`.
- **ORMs are opt-in, not default.** Hand-written SQL via `sqlite3` is the default (see database-conventions skill). When using Supabase or Postgres, Prisma or SQLAlchemy are acceptable — use whichever fits the project. HTTP via `httpx` or `requests`.
- **No standalone servers.** Python that needs hosting goes to Modal (see modal-conventions skill).
- **Config:** `.env` files for keys and config, loaded via `python-dotenv`. Never hardcode keys.
- **YAML config reads:** When loading a YAML config file that has optional sections (e.g., `candidate_profile.yaml`), always use `.get()` chains — never hard dict access — for any key that may be absent. `config["optional_section"]["key"]` crashes with `KeyError` the moment that section hasn't been populated yet. Hard access is only acceptable for sections guaranteed to exist at all times (core required keys). A missing optional section should degrade gracefully, never 500.
- **f-string CSS conditionals:** Never embed Python conditional expressions directly inside CSS attribute values in f-strings — e.g., `f'background:{"var(--rust)" if x else "var(--rule)"};'`. The semicolon after the inner closing `"` is valid CSS but Python's f-string parser hits it before finding `}`, causing a `SyntaxError` that isn't obvious at write time. Pre-compute the value first:
  ```python
  dot_color = "var(--rust)" if x else "var(--rule)"
  f'background:{dot_color};margin-right:2px;'
  ```
- **Syntax gate for Python HTML builders:** After editing any Python file that generates HTML as f-string builders, run `python3 -c "import <module>; print('OK')"` before deploying. Catches SyntaxErrors instantly, costs nothing. Run after every edit block — not just at session end.
- **Jinja2 numeric truthiness trap:** Never guard a numeric display value with `{% if value %}` — `0` and `0.0` evaluate as falsy, silently showing a fallback instead of the real value. Use `{% if value is not none and value != '' %}` for any field that can legitimately be zero. Applies to scores, counts, percentages, and any field populated from a float column.
- **LLM JSON schema notation:** Numeric output fields use bare angle-bracket notation (`<float>`, `<integer>`); string fields use quoted form (`"<string value>"`). One character of quoting changes the model's output type — `<float>` → model returns a float; `"<float>"` → model may return the string `"7.5"`, breaking arithmetic comparisons and Jinja `> N` filters silently. The syntax check passes regardless — this requires human review of any new prompt schema.
