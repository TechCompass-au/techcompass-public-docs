# Compliance Guide

This document is written for compliance officers, risk managers, and auditors evaluating TechCompass for use in regulated financial services environments.

---

## Why Compliance Teams Use TechCompass

Regulatory data access creates compliance obligations. When your team pulls SEC filings or economic indicators for credit decisions, counterparty screening, or investment analysis, you need to be able to answer:

- Who accessed which data?
- When did they access it?
- What was the outcome?
- Can we prove this to an auditor?

TechCompass is built to answer all four questions, out of the box.

---

## Immutable Audit Logs

Every API call generates a tamper-proof audit log entry. For Enterprise customers, logs are stored with Object Lock (WORM) — they cannot be modified or deleted, even by TechCompass staff.

Each log entry captures:

| Field | Description |
|-------|-------------|
| `customer_id` | Identifies the account that made the request |
| `api_key_id` | Identifies which key was used (useful if multiple staff share an account) |
| `endpoint` | Exact path requested (e.g. `/v1/sec/company/320193/facts`) |
| `method` | HTTP method (GET, POST) |
| `status_code` | Response outcome (200, 401, 429, etc.) |
| `timestamp` | UTC timestamp, millisecond precision |
| `request_id` | Unique identifier traceable to server logs |
| `ip_address` | Encrypted (AES-128-CBC). Decryptable for regulatory investigations |

**Example audit log export:**

```json
[
  {
    "timestamp": "2026-05-08T03:42:11.204Z",
    "customer_id": "acme-corp",
    "endpoint": "/v1/sec/company/320193/facts",
    "method": "GET",
    "status_code": 200,
    "request_id": "req_9kXp2mNvQr"
  },
  {
    "timestamp": "2026-05-08T03:42:58.881Z",
    "customer_id": "acme-corp",
    "endpoint": "/v1/fred/series/GDP/data",
    "method": "GET",
    "status_code": 200,
    "request_id": "req_7bYw4jLzTs"
  }
]
```

---

## Responding to an Auditor

**Scenario:** An auditor asks "Show us every time your team accessed SEC data in Q1 2026."

**Step 1:** Call the audit log export endpoint with a date range:

```bash
curl -H "Authorization: ApiKey sk_live_..." \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/v1/audit-logs?start_date=2026-01-01&end_date=2026-03-31"
```

**Step 2:** Review the returned log. Every request is timestamped and attributed to a specific API key.

**Step 3:** Export as JSON and provide to the auditor. Each `request_id` can be cross-referenced with server-side logs if the auditor requires deeper verification.

---

## Data Lineage

TechCompass proxies official government sources. This means:

| Data Source | Official URL | Fetch method |
|-------------|-------------|--------------|
| SEC EDGAR | `https://data.sec.gov/` | HTTPS API, JSON |
| Federal Reserve FRED | `https://fred.stlouisfed.org/` | HTTPS API, JSON |

No transformations are applied to the underlying data. Values returned by TechCompass match values published by the source agency. Cached responses are labelled with fetch timestamps so you can identify the data vintage.

---

## Access Controls

**Per-key revocation**
Each API key can be deactivated independently. If a staff member leaves or a key is compromised, that key is revoked without affecting other users.

**Key rotation**
Keys are rotated by logging in, which deactivates all previous keys and issues a fresh one. This supports periodic rotation policies.

**Customer isolation**
Each customer's data and audit logs are logically isolated. One customer cannot access another's logs or usage data.

**No shared credentials**
TechCompass does not issue shared credentials. Each account receives its own key with its own quota and audit log.

---

## Vendor Risk Register

TechCompass can provide the following for vendor onboarding:

| Document | Availability |
|----------|-------------|
| Company details (legal name, ABN, directors) | On request |
| Security overview | This document and [SECURITY.md](SECURITY.md) |
| Data processing agreement (DPA) | On request (Enterprise) |
| SLA agreement template | On request (Professional / Enterprise) |
| Penetration test summary | On request (Enterprise, after NDA) |
| SOC 2 report | In progress (target Q4 2026) |

Contact [hello@techcompass.com.au](mailto:hello@techcompass.com.au) to request vendor risk documentation.

---

## Versioned APIs and Deprecation Policy

Breaking API changes require a new version path (e.g. `/v2/`). The previous version remains available for a minimum of 90 days after the new version is released, with written deprecation notice to all active customers.

This supports change management requirements in regulated environments where system changes must be pre-approved and tested.

---

## Accountability

TechCompass is an incorporated legal entity. Enterprise customers receive a named account manager, a signed SLA, and a data processing agreement. This satisfies typical vendor accountability requirements in financial services procurement.

---

## Questions

Compliance-related questions: [compliance@techcompass.com.au](mailto:compliance@techcompass.com.au)

General contact: [hello@techcompass.com.au](mailto:hello@techcompass.com.au)
