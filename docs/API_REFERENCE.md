# API Reference

**Base URL:** `https://api-gateway-qyyt3rznka-uc.a.run.app`

All requests and responses use JSON. Authenticated endpoints require the header:

```
Authorization: ApiKey sk_live_YOUR_KEY
```

---

## Authentication

### POST /auth/signup

Create a new account and receive your API key.

**Auth required:** No

**Request body:**

```json
{
  "email": "you@company.com",
  "password": "your-password"
}
```

**Response `201`:**

```json
{
  "user_id": "uuid",
  "api_key": "sk_live_...",
  "tier": "free",
  "quota": 50000
}
```

**Errors:** `409` Email already registered | `429` Too many signup attempts

---

### POST /auth/login

Log in and receive a fresh API key. Previous keys for this account are deactivated.

**Auth required:** No

**Request body:**

```json
{
  "email": "you@company.com",
  "password": "your-password"
}
```

**Response `200`:**

```json
{
  "user_id": "uuid",
  "api_key": "sk_live_...",
  "tier": "free"
}
```

**Errors:** `401` Invalid email or password

---

## User

### GET /user/profile

Return profile details for the authenticated user.

**Auth required:** Yes

**Response `200`:**

```json
{
  "user_id": "uuid",
  "email": "you@company.com",
  "tier": "free",
  "created_at": "2026-05-08T00:00:00Z"
}
```

---

### GET /user/usage

Return API usage statistics for the current billing month.

**Auth required:** Yes

**Response `200`:**

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

### GET /user/usage-history

Return daily call counts from the audit log. Defaults to last 90 days.

**Auth required:** Yes

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `start_date` | string (YYYY-MM-DD) | Optional. Filter from this date |
| `end_date` | string (YYYY-MM-DD) | Optional. Filter to this date |

**Response `200`:**

```json
[
  { "date": "2026-05-07", "calls": 312 },
  { "date": "2026-05-06", "calls": 198 }
]
```

---

## SEC EDGAR

### GET /v1/sec/company/{cik}/facts

Return all XBRL financial facts for a company, sourced directly from SEC EDGAR.

**Auth required:** Yes

**Path parameters:**

| Parameter | Description |
|-----------|-------------|
| `cik` | SEC Central Index Key. Leading zeros optional (e.g. `320193` or `0000320193`) |

**Example:**

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/facts
```

**Response `200`:** Full EDGAR company facts JSON (concept values across all reporting periods)

**Errors:** `404` Company not found

---

### GET /v1/sec/company/{cik}/submissions

Return filing history for a company (10-K, 10-Q, 8-K, etc.).

**Auth required:** Yes

**Path parameters:**

| Parameter | Description |
|-----------|-------------|
| `cik` | SEC Central Index Key |

**Example:**

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/submissions
```

**Response `200`:** Filing list with accession numbers, form types, and dates

---

### GET /v1/sec/search

Full-text search across SEC EDGAR filings.

**Auth required:** Yes

**Query parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `q` | string | Yes | Search query |
| `limit` | integer | No | Max results (default 10) |

**Example:**

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/search?q=annual+report+risk+factors&limit=10"
```

---

## Federal Reserve (FRED)

### GET /v1/fred/series/{series_id}/data

Return observations for a FRED economic data series.

**Auth required:** Yes

**Path parameters:**

| Parameter | Description |
|-----------|-------------|
| `series_id` | FRED series identifier (e.g. `GDP`, `CPIAUCSL`, `FEDFUNDS`) |

**Common series IDs:**

| ID | Description |
|----|-------------|
| `GDP` | Gross Domestic Product |
| `CPIAUCSL` | Consumer Price Index (All Urban, Seasonally Adjusted) |
| `FEDFUNDS` | Federal Funds Effective Rate |
| `UNRATE` | Unemployment Rate |
| `DGS10` | 10-Year Treasury Constant Maturity Rate |

**Example:**

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/GDP/data
```

**Response `200`:**

```json
{
  "series_id": "GDP",
  "observations": [
    { "date": "2025-10-01", "value": "29723.2" },
    { "date": "2025-07-01", "value": "29398.7" }
  ]
}
```

---

### GET /v1/fred/search

Search the FRED series catalog.

**Auth required:** Yes

**Query parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `q` | string | Yes | Search term |
| `limit` | integer | No | Max results (default 10) |

**Example:**

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/search?q=inflation&limit=5"
```

---

## Health

### GET /health

Liveness check. Returns immediately.

**Auth required:** No

**Response `200`:**

```json
{ "status": "ok" }
```

---

### GET /health/ready

Readiness check. Confirms database connectivity.

**Auth required:** No

**Response `200`:**

```json
{ "status": "ready" }
```

---

## Error Codes

| Code | Meaning |
|------|---------|
| `400` | Bad request. Check your request body or query parameters |
| `401` | Missing or invalid API key |
| `403` | Valid key, but endpoint requires a user account (not a legacy key) |
| `404` | Resource not found |
| `409` | Conflict (e.g. email already registered) |
| `422` | Validation error. Response body lists the specific fields |
| `429` | Monthly quota exceeded |
| `500` | Internal server error |
| `502` | Upstream data source temporarily unavailable |
| `503` | Service temporarily unavailable |

All errors return JSON:

```json
{
  "error": "Human-readable message"
}
```
