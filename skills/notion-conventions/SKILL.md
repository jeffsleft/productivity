---
name: notion-conventions
description: Fire when reading or writing Notion via API, syncing data to Notion, building Notion integrations, or handling Notion property types
version: 1.0.0
---

# Notion Integration

- **Writes use both properties and block children.** Database row properties populate the table view; block children populate the deep-dive page. Always populate both when writing — never only one.
- **2000-char block limit:** Notion enforces a 2000-char per-block content limit. When writing long content (LLM output, scraped text), split into multiple paragraph blocks at the 2000-char boundary. Do not truncate.
- **Property type strictness:** Notion is strict about property types. Coerce numbers to `float` before write (not strings); coerce dates to ISO 8601; use `select`/`multi_select` exactly as configured in the DB schema or the write fails with 400.
- **Idempotent writes:** When writing repeatedly to the same row (e.g., updating outcomes), look up by a stable key (SQLite ID stored as a Notion property) rather than relying on title matching.
- **Best-effort sync:** When Notion is a mirror of SQLite (not the SSOT), wrap Notion writes in try/except — log failures, never block the SQLite write.
- **Never use the `notion-client` Python SDK.** Use direct Notion REST API calls via `requests` instead. The SDK has removed and renamed core methods across minor versions (`databases.query()` removed in both v2.7.0 and v3.1.0); an unpinned dep pulls breaking changes on every Modal image rebuild. Write a `_notion_request(method, path, token, **kwargs)` helper using `requests` and use it for all Notion operations. The Notion REST API is stable; the Python SDK is not.
