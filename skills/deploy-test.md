# /deploy-test — Post-Deploy Verification & Documentation

Run this skill after every deploy of the investing-insights web app (or any Modal-hosted app).
**Never report a feature as complete until these checks pass.**

---

## Step 1 — Check Modal logs for startup errors

```bash
modal app logs investing-insights 2>&1 | tail -40
```

Look for any Python tracebacks or `500 Internal Server Error` lines that fired during warmup or the first request. If found: diagnose and fix before proceeding.

---

## Step 2 — Unauthenticated smoke test

```bash
BASE="https://jeffsleft--investing-insights.modal.run"
curl -s "$BASE/health"
```

Expected: `{"status":"ok","db":true}`

If health returns non-200 or db is false: stop, diagnose.

---

## Step 3 — Auth enforcement check

```bash
curl -s -o /dev/null -w "%{http_code}" "$BASE/view/portfolio"
```

Expected: `401`. If 200: auth middleware is broken. If 500: something crashed before auth ran.

---

## Step 4 — Authenticated endpoint test

Requires jeff's password from the Modal secret. If credentials are available locally, run:

```bash
AUTH="jeff:PASSWORD_FROM_MODAL_SECRET"
for ENDPOINT in "/view/portfolio" "/view/journal" "/view/goals" "/view/stress" "/view/ceo" "/view/smart-money" "/view/staleness"; do
  CODE=$(curl -s -o /dev/null -w "%{http_code}" -u "$AUTH" "$BASE$ENDPOINT")
  echo "$CODE  $ENDPOINT"
done
```

Expected: all 200. Any 500 means server-side failure — pull logs and fix.

---

## Step 5 — Test the specific feature just built

For each new endpoint or form added, write a targeted test:

**File upload endpoint:**
```bash
echo "ticker,quant_rating\nAAPL,4.5\nMSFT,4.2" > /tmp/test_sa.csv
curl -s -u "$AUTH" -F "file=@/tmp/test_sa.csv" -F "mode=append" "$BASE/upload/sa-ratings"
```
Expected: success message with ticker count, not a 500.

**Form POST endpoint:**
```bash
curl -s -u "$AUTH" -X POST -F "ticker=AAPL" "$BASE/action/score-ceo"
```
Expected: HTML response (success card or error message), not a 500.

**New GET view:**
```bash
curl -s -u "$AUTH" "$BASE/view/NEW_ENDPOINT" | grep -o "class=\"card\""
```
Expected: at least one card element present.

---

## Step 6 — Pull post-request logs to confirm no hidden errors

After running Step 4–5:
```bash
modal app logs investing-insights 2>&1 | grep -E "ERROR|500|Traceback" | tail -20
```

If anything appears: fix it before reporting done.

---

## Step 7 — Write or update documentation

After a successful test, update `FEATURES.md` in the project root with:

```markdown
## [Feature Name] — YYYY-MM-DD

**What it does:** One sentence.
**Endpoint(s):** POST /upload/sa-ratings
**Who can use it:** jeff only / all users
**How to use:** Step-by-step from the user's perspective.
**Edge cases handled:** list them.
**Known limitations:** list them.
```

If `FEATURES.md` doesn't exist, create it.

---

## Checklist before reporting "done"

- [ ] Health check returns `{"status":"ok","db":true}`
- [ ] Auth returns 401 for unauthenticated requests
- [ ] All existing tabs still return 200 (no regressions)
- [ ] New endpoint returns expected output, not 500
- [ ] Modal logs show no new errors
- [ ] FEATURES.md updated
