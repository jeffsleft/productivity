---
name: gemini-api-conventions
description: Fire when using Gemini API, writing code that calls google.genai, setting model strategy, handling 429/rate-limit errors, or implementing LLM rate limiting
version: 1.0.0
---

# Gemini API & Model Strategy

- **Default to `gemini-2.5-flash` on free tier.** Verified 2026-05-15: on Jeff's API key, the entire pro family (`gemini-pro-latest`, `gemini-2.5-pro`, `gemini-3.1-pro`) and the full `gemini-2.0-flash` series return `limit:0` (zero free-tier allocation, not a rate-throttle — these models are unusable on free tier without billing). What works: `gemini-2.5-flash`, `gemini-2.5-flash-lite`, `gemini-flash-latest`, `gemini-flash-lite-latest`.
- **Never hardcode specific preview versions** like `gemini-3-flash-preview` or `gemini-3.1-pro-preview` — they cause 404s in some regions and are not on free tier.
- **Stable aliases as override hooks:** Make the model configurable via env var (`GEMINI_PRO_MODEL`, `GEMINI_FLASH_MODEL`). Default to `gemini-2.5-flash` so the app runs on free tier out of the box; users with billing enabled override to `gemini-pro-latest` for higher-reasoning paths.
- **Free-tier rate limits (Flash class):** ~5–15 RPM, 250k–1M TPM, several hundred to ~1.5k RPD. Pro class on free tier (when not zero) is much tighter (~2–5 RPM, dozens RPD).
- **Enabling billing buys rate limits more than pro access.** Tier 1 (any payment method attached) jumps flash from ~15 RPM to ~2,000 RPM — a 100x headroom increase that's typically more useful than unlocking pro reasoning. Pro model unlock is a side benefit, not the main reason to enable billing. Set a budget alert at $20/month on `https://ai.dev/rate-limit` before flipping the switch. Typical app costs at moderate use: ~$3/month on `gemini-2.5-flash`, ~$10/month on `gemini-2.5-pro`.
- **Pacing Rule:** Implement a **5-second `time.sleep`** between sequential LLM calls in batch loops. Sleep between rows AND between calls inside a single row (e.g. score → research).
- **Robustness:** Catch `google.genai.errors.ClientError`. On `RESOURCE_EXHAUSTED` (429), exponential backoff starting at 15s: `(15, 30, 60, 120, 240)`. After exhaustion, raise a custom `RateLimitedError` so callers can mark the work item as "re-queue" without surfacing a raw stacktrace to users.
- **Diagnose `limit:0` symptoms with a probe.** When 429s persist after correct pacing + backoff, write a `probe_model_quota` Modal function (or local script) that hits every candidate model with a 1-token prompt and reports which ones succeed vs 429. `limit:0` is account/billing-level, not code-fixable — direct the user to https://ai.dev/rate-limit. Pattern reference: `recruiting-engine/app/main.py::probe_model_quota`.
- **Gemma is not a Gemini drop-in.** Gemma 3/4 models have the most generous free-tier quota (~30 RPM, thousands RPD), but they lack tool use (no Google Search grounding) and have patchier JSON-mode support. Use only for plain-text LLM calls, never for `web_search=True` flows.
