# Product Roadmap

Our roadmap is driven by the needs of compliance, risk, and finance teams operating in regulated markets. We are building the compliance data layer for Australian and US financial services.

> Dates are estimates. Roadmap priorities are subject to change based on customer feedback.

---

## Phase 1 — Foundation (Current, May 2026)

**Status: Live**

- SEC EDGAR company facts, submissions, and full-text search
- Federal Reserve FRED: 200,000+ economic series
- API key authentication with monthly quota enforcement
- Encrypted, per-request audit logging
- Stripe-powered self-service billing (Free and Professional tiers)
- TechCompass-branded web frontend (signup, dashboard, upgrade flow)
- GCP Cloud Run hosting (us-central1, auto-scaling)

---

## Phase 2 — Australian Expansion (Weeks 2-4)

**Planned: May - June 2026**

- **AU ABR (Australian Business Register):** ABN lookup, entity name, GST status, business structure
- **ASIC Company Search:** Company registration, officeholders, charges, document history
- **Sydney regional hosting:** `ap-southeast-2` deployment for Australian data residency requirements
- Regional API keys: Optionally pin your account to AU hosting for compliance obligations

---

## Phase 3 — US Market Deepening (Weeks 4-8)

**Planned: June - July 2026**

- **US Stock Data API:** Real-time and historical price data, corporate actions (adjusted for splits and dividends)
- **SEC Compliance and Enforcement API:** Litigation releases, administrative proceedings, whistleblower data, enforcement actions (EDGAR full-text search expansion)
- **Beneficial Ownership Data:** FinCEN BOI filing data (subject to regulatory access rules)

---

## Phase 4 — Australian Registries (Weeks 8-12)

**Planned: July - August 2026**

- **AU Land Titles:** Property ownership data (state-by-state, starting with NSW and VIC)
- **State Business Registries:** Licensing data, professional registrations, regulatory sanctions
- **APRA data feeds:** Authorised deposit-taking institutions, insurance licensees

---

## Phase 5 — Intelligence Layer (Months 3-6)

**Planned: Q3 2026**

- **Entity Resolution API:** Match entities across data sources (ABN to SEC CIK, name variations, subsidiary mapping)
- **Compliance Check API:** Single endpoint to screen a company across SEC enforcement, ASIC actions, ABR status, and sanctions lists
- **Webhook support:** Push notifications when monitored companies file new documents or trigger compliance events

---

## Phase 6 — Compliance Platform (Months 6-12)

**Planned: Q4 2026**

- **Compliance Dashboard:** Web-based monitoring interface for compliance teams (no API required)
- **Filing Monitors:** Subscribe to SEC/ASIC filing alerts for a watchlist of entities
- **BI Integrations:** Power BI, Tableau, and Looker connectors for audit log and usage analytics
- **SOC 2 Type II certification:** Third-party audit completion and public report

---

## Phase 7 — Vertical SaaS (12+ months)

**Planned: 2027**

- **Fintech underwriting:** Embedded compliance checks for lending and payment platforms
- **Investor KYC/AML workflows:** Integrated KYC pipeline with entity resolution, sanctions screening, and document generation
- **White-label options:** Reseller and OEM agreements for compliance software vendors

---

## Feedback

Roadmap priorities are shaped by customer demand. If you need a specific data source or workflow sooner, raise it via:

- GitHub Issues on this repository
- Email: [hello@techcompass.com.au](mailto:hello@techcompass.com.au)

Features requested by multiple customers are accelerated.
