# curl Request Examples

All examples use `sk_live_YOUR_KEY` as a placeholder. Replace it with your actual API key.

**Base URL:** `https://api-gateway-qyyt3rznka-uc.a.run.app`

---

## Account Management

### Sign Up

```bash
curl -X POST https://api-gateway-qyyt3rznka-uc.a.run.app/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email": "you@company.com", "password": "your-secure-password"}'
```

**Response:**
```json
{
  "user_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "api_key": "<your-api-key>",
  "tier": "free",
  "quota": 50000
}
```

---

### Log In (rotates your API key)

```bash
curl -X POST https://api-gateway-qyyt3rznka-uc.a.run.app/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "you@company.com", "password": "your-secure-password"}'
```

---

## User

### Check Usage

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/user/usage
```

**Response:**
```json
{
  "calls_this_month": 1024,
  "quota_limit": 50000,
  "percent_used": 2.0,
  "reset_date": "2026-06-01",
  "tier": "free"
}
```

---

### Get Profile

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/user/profile
```

---

### Usage History (last 90 days)

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/user/usage-history
```

### Usage History (date range)

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/user/usage-history?start_date=2026-05-01&end_date=2026-05-31"
```

---

## SEC EDGAR

### Get Company Facts (Apple Inc., CIK: 320193)

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/facts
```

### Get Company Facts (Microsoft, CIK: 789019)

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/789019/facts
```

### Get Filing Submissions (Apple Inc.)

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/submissions
```

### Search SEC Filings

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/search?q=risk+factors+cybersecurity&limit=10"
```

---

## Federal Reserve (FRED)

### Get GDP Data

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/GDP/data
```

### Get CPI (Inflation) Data

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/CPIAUCSL/data
```

### Get Federal Funds Rate

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/FEDFUNDS/data
```

### Get Unemployment Rate

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/UNRATE/data
```

### Search FRED Series

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/search?q=inflation&limit=5"
```

---

## Health Check

```bash
curl https://api-gateway-qyyt3rznka-uc.a.run.app/health
```

---

## Handling Errors

### Quota Exceeded (429)

```bash
# Response when you've exceeded your monthly limit:
{
  "error": "Monthly quota exceeded",
  "quota": 50000,
  "tier": "free",
  "upgrade_url": "/billing/upgrade-to-pro"
}
```

The `Retry-After` header will indicate when the quota resets.

### Invalid Key (401)

```bash
# Response for missing or invalid Authorization header:
{
  "error": "Invalid API key"
}
```

### Pretty-print JSON responses

Pipe through `jq` if installed:

```bash
curl -s -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/GDP/data | jq .
```
