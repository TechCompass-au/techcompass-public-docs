# Changelog

All notable changes to the TechCompass API Gateway are documented here.

---

## v1.1.0 — 14 May 2026

### New Data APIs

**US — Sanctions Screening**
- `GET /v1/us/sanctions/ofac/screen` — Screen an entity name against the OFAC Specially Designated Nationals (SDN) list. Returns match confidence, entity type, and programme tags. Every screen creates an immutable audit record before the response is returned.
- `GET /v1/us/sanctions/ofac/entity/{uid}` — Retrieve a specific SDN entry by UID
- `GET /v1/us/sanctions/ofac/list-version` — Return the current SDN list date ingested

**US — Enforcement & Disciplinary**
- `GET /v1/us/enforcement/sec` — Search SEC litigation releases and enforcement actions
- `GET /v1/us/enforcement/sec/{release_number}` — Retrieve a specific SEC enforcement release
- `GET /v1/us/enforcement/finra/broker/{crd}` — Look up an individual broker via FINRA BrokerCheck by CRD number
- `GET /v1/us/enforcement/finra/firm/{crd}` — Look up a registered firm via FINRA BrokerCheck
- `GET /v1/us/enforcement/search` — Cross-source enforcement search (SEC + FINRA combined)

**AU — Entity Intelligence**
- `GET /v1/au/abr/{abn}` — Look up an Australian Business Number (ABN) via the ABR. Returns entity details, GST registration status, business names, and KYC signals
- `GET /v1/au/asic/{acn}` — Look up an Australian Company Number (ACN) from the ASIC register. Includes paired ABR data and KYC risk signals
- `GET /v1/au/entity/search` — Search Australian entities by company name, ABN, or ACN across ASIC and ABR simultaneously

**AU — Financial Advisers Register**
- `GET /v1/au/advisers/{adv_number}` — Retrieve a financial adviser by ASIC adviser number
- `GET /v1/au/advisers/search` — Search advisers by name with optional status filter
- `GET /v1/au/advisers/licence/{lic_number}` — List all advisers registered under an AFS licence number

**AU — Climate Disclosure**
- `GET /v1/au/climate/nga-factors` — Query Australian National Greenhouse Accounts (NGA) emission factors by activity, fuel type, or state. Required for Scope 1 emissions reporting under ASRS
- `GET /v1/au/climate/nga-factors/versions` — List available NGA workbook versions

### Infrastructure

- Australian regional deployment on Google Cloud Run (`australia-southeast1`) for data residency compliance
- Dedicated Cloud SQL instance for AU data (`australia-southeast1`) — ASIC company register (~4.3M records), ABR entities, financial advisers, NGA emission factors
- Automated data pipelines:
  - ASIC company register: weekly refresh (Monday 23:00 UTC)
  - ASIC Financial Advisers Register: weekly refresh (Thursday 00:00 UTC)
  - ABR entity cache: hourly refresh
  - OFAC SDN list: daily refresh (08:00 UTC)
  - SEC enforcement actions: daily refresh (06:00 UTC)
  - NGA emission factors: annual refresh (1 Feb)

### Security

- All AU data API calls write an immutable audit log row to the AU Cloud SQL instance before the response is returned
- KYC signals (`overall_risk`, `flags`) returned on all AU entity and financial adviser lookups
- Data lineage block on every response — source, source URL, licence, snapshot date

---

## v1.0.0 — 8 May 2026

Initial public release.

### Data APIs
- SEC EDGAR company facts endpoint (`/v1/sec/company/{cik}/facts`)
- SEC EDGAR company submissions endpoint (`/v1/sec/company/{cik}/submissions`)
- Federal Reserve FRED series data (`/v1/fred/series/{series_id}/data`)
- Federal Reserve FRED series metadata (`/v1/fred/series/{series_id}`)

### Authentication and Access
- API key authentication (`sk_live_` prefix, SHA-256 hashed at rest)
- Secure signup and login endpoints
- Monthly quota enforcement by tier (Free: 50,000 calls, Professional: 500,000 calls)
- Automatic quota reset on the 1st of each month

### Compliance Features
- Encrypted audit logging on every API request (customer ID, endpoint, status, timestamp, IP encrypted with Fernet)
- Request tracing (`X-Request-ID` header on all responses)
- No PII or sensitive data in application logs
- Generic error messages (no internal stack traces exposed)

### Billing
- Stripe-powered subscription management
- Self-service upgrade to Professional tier via hosted Stripe Checkout
- Billing portal for invoice download and payment method management
- Webhook-driven tier upgrade with idempotent event processing

### Frontend
- TechCompass-branded signup, login, dashboard, and upgrade pages
- API key display with copy-to-clipboard (shown once at signup)
- Real-time usage meter with monthly progress bar
- Mobile-responsive layout

### Infrastructure
- Hosted on Google Cloud Run (us-central1)
- Containerised via Docker, deployed via Cloud Build CI/CD
- Secrets managed in GCP Secret Manager (no credentials in source)
- PostgreSQL on Cloud SQL (asyncpg driver)

---

*Roadmap for upcoming releases: [docs/ROADMAP.md](docs/ROADMAP.md)*
