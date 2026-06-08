# API Reference

**Base URL:** `https://api.techcompass.com.au`

All requests and responses use JSON. Authenticated endpoints require the header:

```
Authorization: ApiKey sk_live_YOUR_KEY
```

Every response includes an `X-Request-ID` header for tracing. All data API responses include a `data_lineage` block with source attribution, licence, and retrieval timestamp.

---

## Authentication

### POST /auth/signup

Create a new account and receive your API key.

**Auth required:** No

**Request body:**

```json
{ "email": "you@company.com", "password": "your-password" }
```

**Response `201`:**

```json
{ "user_id": "uuid", "api_key": "sk_live_...", "tier": "free", "quota": 50000 }
```

**Errors:** `409` Email already registered | `429` Too many signup attempts

---

### POST /auth/login

Log in and receive a fresh API key.

**Auth required:** No

**Request body:**

```json
{ "email": "you@company.com", "password": "your-password" }
```

**Response `200`:**

```json
{ "user_id": "uuid", "api_key": "sk_live_...", "tier": "free" }
```

**Errors:** `401` Invalid email or password

---

## User

### GET /user/profile

**Auth required:** Yes

**Response `200`:**

```json
{ "user_id": "uuid", "email": "you@company.com", "tier": "free", "created_at": "2026-05-08T00:00:00Z" }
```

---

### GET /user/usage

Return API usage for the current billing month.

**Auth required:** Yes

**Response `200`:**

```json
{ "calls_this_month": 1024, "quota_limit": 50000, "percent_used": 2.0, "reset_date": "2026-06-01", "tier": "free" }
```

---

### GET /user/usage-history

Return daily call counts from the audit log. Defaults to last 90 days.

**Auth required:** Yes

**Query parameters:** `start_date` (YYYY-MM-DD), `end_date` (YYYY-MM-DD)

---

## US - SEC EDGAR

### GET /v1/sec/company/{cik}/facts

Return all XBRL financial facts for a company from SEC EDGAR.

**Auth required:** Yes | **Cache:** 1 hour

| Parameter | Description |
|-----------|-------------|
| `cik` | SEC Central Index Key. Leading zeros optional (`320193` or `0000320193`) |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api.techcompass.com.au/v1/sec/company/320193/facts
```

**Errors:** `404` Company not found | `503` SEC EDGAR temporarily unavailable

---

### GET /v1/sec/filings

Return recent filings for a company filtered by form type.

**Auth required:** Yes | **Cache:** 1 hour

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `cik` | string | Yes | Company CIK |
| `form_type` | string | No | Form type filter -- default `10-K` |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api.techcompass.com.au/v1/sec/filings?cik=320193&form_type=10-Q"
```

---

### GET /v1/sec/company/{cik}/submissions

Return the full submissions history for a company.

**Auth required:** Yes | **Cache:** 1 hour

---

## US - Federal Reserve (FRED)

### GET /v1/fred/series/{series_id}

Return metadata for a FRED economic series.

**Auth required:** Yes | **Cache:** 15 minutes

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api.techcompass.com.au/v1/fred/series/GDP
```

---

### GET /v1/fred/series/{series_id}/data

Return observations (data points) for a FRED series.

**Auth required:** Yes | **Cache:** 15 minutes

| Parameter | Type | Description |
|-----------|------|-------------|
| `observation_start` | YYYY-MM-DD | Optional start date filter |
| `observation_end` | YYYY-MM-DD | Optional end date filter |

**Common series IDs:**

| ID | Description |
|----|-------------|
| `GDP` | Gross Domestic Product |
| `CPIAUCSL` | Consumer Price Index (All Urban) |
| `FEDFUNDS` | Federal Funds Effective Rate |
| `UNRATE` | Unemployment Rate |
| `DGS10` | 10-Year Treasury Rate |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api.techcompass.com.au/v1/fred/series/FEDFUNDS/data?observation_start=2024-01-01"
```

---

## US - OFAC Sanctions Screening

Every screening call creates an immutable audit record before the response is returned.

### GET /v1/us/sanctions/ofac/screen

Screen an entity name against the OFAC Specially Designated Nationals (SDN) list.

**Auth required:** Yes

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Entity name to screen |
| `entity_type` | string | No | Filter by type: `Individual`, `Entity`, `Vessel`, `Aircraft` |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api.techcompass.com.au/v1/us/sanctions/ofac/screen?name=Acme+Trading+Co"
```

**Response `200`:**

```json
{
  "data": {
    "query_name": "Acme Trading Co",
    "match_count": 0,
    "matches": []
  },
  "meta": { "request_id": "uuid", "cached": false },
  "data_lineage": {
    "source": "OFAC SDN List",
    "source_url": "https://home.treasury.gov/policy-issues/financial-sanctions/specially-designated-nationals-and-blocked-persons-list-sdn-human-readable-lists",
    "sdn_list_date": "2026-05-13",
    "retrieved_at": "2026-05-14T09:00:00Z",
    "request_id": "uuid"
  }
}
```

---

### GET /v1/us/sanctions/ofac/entity/{uid}

Retrieve a specific SDN list entry by UID.

**Auth required:** Yes

---

### GET /v1/us/sanctions/ofac/list-version

Return the current SDN list date loaded into the system.

**Auth required:** Yes

---

## US - Enforcement and Disciplinary

### GET /v1/us/enforcement/sec

Search SEC litigation releases and enforcement actions.

**Auth required:** Yes

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Search term |
| `limit` | integer | Max results (default 20) |

---

### GET /v1/us/enforcement/sec/{release_number}

Retrieve a specific SEC enforcement release.

**Auth required:** Yes

---

### GET /v1/us/enforcement/finra/broker/{crd}

Look up an individual broker via FINRA BrokerCheck.

**Auth required:** Yes

| Parameter | Description |
|-----------|-------------|
| `crd` | FINRA CRD number for the individual |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api.techcompass.com.au/v1/us/enforcement/finra/broker/1234567
```

**Response includes:** registration history, disclosure events, qualifications, employment history, and KYC risk signals.

**Data lineage note:** FINRA data is provided under FINRA permitted-use conditions. Every response includes a `data_lineage.downstream_use_restriction` field. Commercial redistribution requires separate FINRA approval.

---

### GET /v1/us/enforcement/finra/firm/{crd}

Look up a registered firm via FINRA BrokerCheck.

**Auth required:** Yes

---

### GET /v1/us/enforcement/search

Cross-source enforcement search across SEC and FINRA simultaneously.

**Auth required:** Yes

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Entity or individual name |

---

## AU - Entity Intelligence

AU data calls are audited against the AU Cloud SQL instance (australia-southeast1). All responses include `data_lineage.source_asic_snapshot_date` and a `kyc` block with `overall_risk` and `flags`.

### GET /v1/au/abr/{abn}

Look up an Australian Business Number (ABN) via the ABR.

**Auth required:** Yes

| Parameter | Description |
|-----------|-------------|
| `abn` | 11-digit ABN. Spaces optional |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api.techcompass.com.au/v1/au/abr/51824753556
```

**Response includes:** entity name, entity type, GST registration status, business names, state, paired ASIC record (if available), and KYC signals.

**Errors:** `422` ABN not 11 digits | `503` ABR upstream unavailable

---

### GET /v1/au/asic/{acn}

Look up a company by Australian Company Number (ACN) from the ASIC register.

**Auth required:** Yes

| Parameter | Description |
|-----------|-------------|
| `acn` | 9-digit ACN. Spaces optional |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  https://api.techcompass.com.au/v1/au/asic/004028077
```

**Response includes:** company name, type, class, registration status, registration and deregistration dates, paired ABR record (if available), and KYC signals.

**Data source:** ASIC Company Register weekly snapshot (~4.3M companies) via data.gov.au. Licence: CC BY 3.0 AU.

---

### GET /v1/au/entity/search

Search Australian entities by company name, ABN, or ACN.

**Auth required:** Yes

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `q` | string | Yes | Name (min 2 chars), 11-digit ABN, or 9-digit ACN |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api.techcompass.com.au/v1/au/entity/search?q=Commonwealth+Bank"
```

**Response:** Up to 20 matching entities from ASIC and ABR, each with KYC signals.

---

## AU - Financial Advisers Register

Data sourced from the ASIC Financial Advisers Register (FAR) -- weekly snapshot. Licence: CC BY 3.0 AU.

### GET /v1/au/advisers/{adv_number}

Retrieve a financial adviser by ASIC adviser number.

**Auth required:** Yes

**Response includes:** adviser name, role status, AFS licence details, registration history, qualifications, CPD compliance, and KYC signals.

---

### GET /v1/au/advisers/search

Search advisers by name with optional status filter.

**Auth required:** Yes

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Adviser name (min 2 chars) |
| `status` | string | Optional. Filter by role status |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api.techcompass.com.au/v1/au/advisers/search?q=Smith&status=Current"
```

---

### GET /v1/au/advisers/licence/{lic_number}

List all advisers registered under an AFS licence number.

**Auth required:** Yes

---

## AU - Climate Disclosure

Emission factors from the Australian National Greenhouse Accounts (NGA), published by DCCEEW. Required for Scope 1 emissions reporting under the Australian Sustainability Reporting Standards (ASRS). Licence: CC BY 4.0.

### GET /v1/au/climate/nga-factors

Query NGA emission factors.

**Auth required:** Yes

| Parameter | Type | Description |
|-----------|------|-------------|
| `activity` | string | Optional. Filter by activity category |
| `fuel_type` | string | Optional. Filter by fuel type |
| `state` | string | Optional. AU state code (NSW, VIC, QLD, SA, WA, TAS, NT, ACT) |

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api.techcompass.com.au/v1/au/climate/nga-factors?fuel_type=Natural+Gas&state=NSW"
```

**Response includes:** `emission_factor`, `unit`, `publication_reference`, `workbook_version`, and full data lineage.

---

### GET /v1/au/climate/nga-factors/versions

List available NGA workbook versions in the system.

**Auth required:** Yes

---

## Health

### GET /health

Liveness check. Returns immediately.

**Auth required:** No

```json
{ "status": "ok" }
```

### GET /health/ready

Readiness check. Confirms database connectivity.

**Auth required:** No

---

## Response Envelope

All data API responses follow this structure:

```json
{
  "data": {},
  "meta": {
    "request_id": "uuid",
    "cached": false,
    "upstream_latency_ms": 142
  },
  "data_lineage": {
    "source": "Source name",
    "source_url": "https://...",
    "license": "Licence text",
    "retrieved_at": "2026-05-14T09:00:00Z",
    "request_id": "uuid"
  }
}
```

AU entity responses additionally include:

```json
{
  "kyc": {
    "overall_risk": "low",
    "flags": []
  }
}
```

---

## Error Codes

| Code | Meaning |
|------|---------|
| `400` | Bad request |
| `401` | Missing or invalid API key |
| `403` | Endpoint requires authenticated user account |
| `404` | Resource not found |
| `409` | Conflict (e.g. email already registered) |
| `422` | Validation error -- response lists specific fields |
| `429` | Monthly quota exceeded |
| `500` | Internal server error |
| `502` | Upstream data source error |
| `503` | Service or upstream temporarily unavailable |

All errors return:

```json
{ "error": "Human-readable message" }
```
