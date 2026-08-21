---
name: scraping-conventions
description: Fire when web scraping, data fetching from ATS boards, using Jina/Firecrawl/BeautifulSoup/curl_cffi, fetching job descriptions, or handling bot detection
version: 1.0.0
---

# Web Scraping & Data Fetching

- **API-First Discovery:** When fetching job descriptions from major boards (Greenhouse, Lever, Ashby), **never** use basic BeautifulSoup scrapers. They are blocked (403). Use their official public APIs/GraphQL endpoints.
- **User-Agent:** For fallback scraping on generic sites, always use a modern browser `User-Agent` header.
- **TLS-fingerprinted sites:** When a target uses Akamai or similar bot detection that blocks standard HTTP clients, use `curl_cffi` with Chrome TLS impersonation (`impersonate="chrome124"` or current). Pin to `curl_cffi>=0.15.0` (CVE-2026-33752).
- **JS-free content for LLM input:** Prefer Jina Reader (`r.jina.ai/<url>`) over BeautifulSoup or Playwright when the goal is readable text for downstream LLM processing. Clean Markdown output, no Chrome rendering required. Requires `JINA_API_KEY`.
- **Firecrawl as JS-rendered fallback:** Jeff has a firecrawl.dev account. Use `firecrawl-py` as a fallback after Jina when the target page is JS-rendered (Workday, Rippling, iCIMS, etc.) and Jina returns thin content (<300 chars). Standard chain: ATS API → Jina → Firecrawl → BeautifulSoup. Key stored as `FIRECRAWL_API_KEY` in the project's Modal secret. Free tier: 500 scrapes/month — sufficient for personal-scale tools.
- **Content caps for LLM input:** Cap scraped content at ~30k chars before passing to an LLM. Prevents token bloat and stays inside most models' context comfortably.
- **Content length alone doesn't prove a fetch succeeded.** Expired/removed postings on Greenhouse and Google Careers don't 404 — they silently redirect to a generic jobs-listing or search-results page that easily clears any length threshold, producing a confident-looking false positive. Check for explicit dead-link signals before trusting a fetch: Greenhouse appends `?error=true` to the redirect URL; Google Careers renders literal "Job not found" text. Don't rely on `len(content) > N` as the only success check for ATS-sourced JD fetches.
- **Authenticated browser automation is a different capability than headless scraping, not a slower version of it.** For sites that block server-side fetchers by design (e.g. LinkedIn job pages), a real logged-in Chrome session via Claude-in-Chrome can read content a backend scraper never will — it's the user's own browsing, not a bot. Not fully reliable per-listing (some postings don't render inline description content regardless), so treat it as a manual/semi-automated fallback for individual URLs, not a batch-scraping replacement.
