---
name: frontend-htmx-conventions
description: Fire when building HTMX + Jinja2 frontends, writing web routes that HTMX calls, handling redirects in web apps, building interactive UIs
version: 1.0.0
---

# Frontend Conventions

- **Static UIs as self-contained `.html` files** when the tool is simple and shareable — no build step, no `npm install`, no bundler. Opens directly in a browser. Tailwind CDN is fine for styling.
- **HTMX is the default for interactive UIs.** Prefer HTMX + server-rendered Jinja2 templates over React/Vue for most tools. React or Vue are acceptable when the project clearly warrants it (e.g., complex client-side state, component-heavy UI, or an off-the-shelf component library).
- **HTMX follows 302 redirects and swaps the full response into the target element.** Never return `RedirectResponse` from an HTMX-triggered route — HTMX transparently follows the redirect and swaps the full-page HTML (sidebar, topbar, `<html>`) into `hx-target`, producing a broken "picture-in-picture" layout with no console error. Return an HTML fragment instead. If the route must also handle plain form POSTs, branch on the `HX-Request` header: HTMX caller → return fragment; plain POST → return `RedirectResponse`.
- **Never use a partial endpoint URL as an `<a href>` target in any HTMX response fragment.** Partial endpoints return fragment HTML only — no `<html>`, no base layout, no CSS variables resolved. Clicking an `<a href="/partial/endpoint">` in an HTMX response triggers full-page navigation to a bare, unstyled fragment. Any inline success/error message that links elsewhere must link to a full-page route (e.g. `/discovered`, not `/discovered/{id}/detail`). Check every route that returns `RedirectResponse` — each is a latent picture-in-picture bug waiting to be triggered via HTMX.
- **Hosting target:** Cloudflare Workers/Pages for anything shareable (see cloudflare-stack-rules skill).
