---
name: cloudflare-stack-rules
description: Fire when working with Cloudflare Workers/Pages/KV/D1, wrangler.toml, Worker routing, configuring KV namespaces, or deploying to Cloudflare
version: 1.0.0
---

# Cloudflare Workers

- **Architecture pattern:** Worker handles routing, auth, and static HTML; offload Python work (LLM calls, scraping, anything needing native deps) to Modal. Worker → Modal HTTPS, never the reverse.
- **Config:** Use `wrangler.toml` with explicit `compatibility_date`. Pin KV namespace IDs in `[[kv_namespaces]]` — never hardcode IDs in Worker code.
- **Secrets:** Always via `wrangler secret put` and accessed as `env.SECRET_NAME`. Never commit secrets to `wrangler.toml` or source. Rotate any secret accidentally committed to git history before flipping a repo public.
- **Auth:** For single-user tools, gate all API routes with a shared-secret header check (e.g. `X-Passcode` against `env.PASSCODE`). Return JSON `{ error: "Unauthorized" }` with status 401 — never leak which routes exist.
- **KV usage:** Values are JSON-serialized strings; always `JSON.stringify` on write and `JSON.parse` on read with a sensible default (`raw ? JSON.parse(raw) : []`). KV is eventually consistent — do not use it for write-then-immediately-read flows.
- **Deployment:** `npx wrangler login` once, then `npx wrangler deploy` from the project root. Custom domains attached via Workers Routes in the Cloudflare dashboard after first deploy.
- **Pinning the Modal endpoint:** Store the Modal URL as a top-of-file constant in `worker.js` (e.g. `const MODAL_URL = '...';`). When redeploying the Modal function, update this constant.
