---
name: pre-ship-security-checklist
description: Pre-deployment security checklist for Jeff's Python/Modal/HTMX/Cloudflare stack. Run this skill before deploying any new app or major feature to verify all 12 security layers are in place — secrets, auth, CSRF, XSS, headers, file uploads, input validation, SQLite, Notion API, Cloudflare, dependencies, and LLM calls. Use this skill whenever Jeff is about to run `modal deploy`, ship a new endpoint, or finishes building a new app. Distinct from `/security-review` (which reviews a git diff) — this is a full pre-ship architecture checklist.
---

# Pre-Ship Security Checklist

Run this skill when finishing a new app or adding a significant feature. Work through each section and flag issues before reporting done. Apply fixes inline — don't just note them.

**Stack defaults:** Python 3.12 · Modal (serverless) · FastAPI · HTMX · Cloudflare (front-door) · SQLite on Modal Volume · GitHub private repo · Anthropic API for AI calls.

---

## 1. Secrets & Credentials

- [ ] No hardcoded API keys, passwords, or tokens anywhere in the code
- [ ] All secrets loaded from environment variables or Modal Secrets (`modal.Secret.from_name(...)`)
- [ ] `.env` and any local secret files are in `.gitignore`
- [ ] Modal secret names are documented in the project README or HANDOFF.md (names only, not values)
- [ ] Anthropic API key is separate from any Claude Pro subscription — must be in `console.anthropic.com`

**Supply chain note:** When adding a new `pip_install()` dependency to a Modal image, prefer packages with >1M monthly downloads and active maintenance. Run `pip-audit` locally before deploying if adding anything new:
```bash
pip install pip-audit && pip-audit -r requirements.txt
```

---

## 2. Authentication

- [ ] All non-health endpoints are protected with `Depends(_verify)` or equivalent
- [ ] `/health` is intentionally public — confirm it exposes no sensitive data
- [ ] Auth uses `secrets.compare_digest` (not `==`) to prevent timing attacks
- [ ] Write-gated actions check `username == "jeff"` (or equivalent) explicitly
- [ ] No credentials are logged at any level

---

## 3. CSRF Protection

- [ ] `CSRFMiddleware` is registered in `build_app()` (checks `X-Requested-With: XMLHttpRequest` on all POST/PUT/DELETE/PATCH)
- [ ] The shell HTML `<body>` tag includes `hx-headers='{"X-Requested-With": "XMLHttpRequest"}'` so HTMX sends the header automatically
- [ ] Any non-HTMX form that POSTs (e.g. a plain HTML form) must add the header manually or use a different CSRF token approach

---

## 4. XSS Prevention

- [ ] All user-supplied or external data rendered in HTML goes through `_e()` (html.escape with quote=True)
- [ ] Links from external data go through `_safe_url()` (enforces https:// prefix)
- [ ] No `| safe` filters, `mark_safe()`, or raw HTML injection of untrusted content
- [ ] Alpine.js expressions do not interpolate unsanitized strings from server data

---

## 5. Security Headers

Confirm `SecurityHeadersMiddleware` is setting all of these:

| Header | Expected Value |
|--------|---------------|
| X-Content-Type-Options | nosniff |
| X-Frame-Options | DENY |
| Referrer-Policy | no-referrer |
| Strict-Transport-Security | max-age=63072000; includeSubDomains |
| Content-Security-Policy | (see modal_app.py — unsafe-eval required for Alpine.js v3) |

---

## 6. File Uploads

- [ ] Upload endpoints check `content-length` header before reading
- [ ] Upload endpoints check actual byte length after `await file.read()` (header can be spoofed)
- [ ] Size limit is 5 MB (`_MAX_UPLOAD_BYTES = 5 * 1024 * 1024`) — lower for smaller expected types (CSV: 1 MB)
- [ ] File extension is validated before parsing (`.xml`, `.xlsx`, `.csv` only)
- [ ] No shell commands or `subprocess` calls use filenames or file content

---

## 7. Input Validation

- [ ] Ticker symbols validated with `_TICKER_RE = re.compile(r"^[A-Z0-9.\-]{1,10}$")` before use in DB queries or API calls
- [ ] URL path parameters are type-annotated so FastAPI validates them
- [ ] Query parameters are not interpolated directly into SQL — use parameterized queries (`conn.execute("... WHERE x = ?", (val,))`)
- [ ] Form fields used in external API calls are sanitized or validated before passing

---

## 8. SQLite (Modal Volume)

- [ ] Connections use `sqlite3.connect(str(DB_PATH), timeout=10)`
- [ ] WAL mode enabled: `conn.execute("PRAGMA journal_mode=WAL")`
- [ ] All connections use context managers (`with _db() as conn`) or explicit close in `finally`
- [ ] No raw string interpolation into SQL — parameterized queries only

**Concurrency note:** Modal can spin up multiple containers. WAL mode + 10s timeout reduces locking errors. If concurrent writes become a real issue, migrate to Neon (Postgres) via Cloudflare Hyperdrive.

---

## 9. Notion API

- [ ] `NOTION_TOKEN` is only accessed server-side; never returned to the client
- [ ] All Notion queries use structured JSON filter bodies (not string-interpolated filter values)
- [ ] `_safe_url()` is applied to any Notion page URL before rendering as an `<a href>`

---

## 10. Cloudflare Front-Door

- [ ] App is proxied through Cloudflare (not direct Modal URL in production)
- [ ] Modal app URL is not published or linked publicly — Cloudflare is the only entry point users know
- [ ] If using `CF-Connecting-IP` for logging, confirm Cloudflare is actually in front (otherwise header can be spoofed)

---

## 11. Dependency Hygiene

When adding new packages to `modal_app.py`'s `image.pip_install(...)`:

1. Check PyPI for last publish date and download count — avoid packages with <6 months of activity
2. Pin to a specific version when the package touches auth, crypto, or file parsing
3. Run `pip-audit` locally before deploying
4. Never `pip install` from git URLs or unofficial mirrors in the Modal image definition

---

## 12. LLM / Anthropic API Calls

- [ ] `ANTHROPIC_API_KEY` is from Modal Secret, not hardcoded
- [ ] LLM-calling endpoints are write-gated (`username == "jeff"`) — not exposed to all users
- [ ] Prompts do not include raw user input that could be used for prompt injection
- [ ] Model is pinned to a specific version string (not `"claude-latest"` or similar floating aliases)
- [ ] `max_tokens` is set to a reasonable cap on every call

---

## Final checklist before shipping

- [ ] Run `/deploy-test` after any security change — confirm auth still enforces (401 for unauth requests)
- [ ] Confirm all POST endpoints return 403 when called without `X-Requested-With: XMLHttpRequest` (CSRF check working)
- [ ] No new secrets appear in `git diff` — run `git diff HEAD` and scan manually before committing
- [ ] `FEATURES.md` updated if new endpoints were added
- [ ] Private GitHub repo exists under https://github.com/jeffsleft/ and code is pushed
