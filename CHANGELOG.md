# Changelog

All notable changes to the TechCompass API Gateway are documented here.

---

## v1.0.0 — 8 May 2026

Initial public release.

### Data APIs
- SEC EDGAR company facts endpoint (`/v1/sec/company/{cik}/facts`)
- SEC EDGAR company submissions endpoint (`/v1/sec/company/{cik}/submissions`)
- SEC EDGAR full-text search (`/v1/sec/search`)
- Federal Reserve FRED series data (`/v1/fred/series/{series_id}/data`)
- Federal Reserve FRED series search (`/v1/fred/search`)

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
