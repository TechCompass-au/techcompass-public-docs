# Security

This document describes the security controls in place for the TechCompass API Gateway.

---

## Authentication

**API key scheme**
All API requests are authenticated with a key in the format `sk_live_<43-char-random>`. Keys are generated using a cryptographically secure random function (`secrets.token_urlsafe(32)`) providing 256 bits of entropy.

**Key storage**
API keys are never stored in plaintext. Only the SHA-256 hash of each key is persisted in the database. The plaintext key is shown to the customer once at signup or login and is not recoverable by TechCompass.

**Key rotation**
Logging in generates a fresh API key and deactivates all previous keys for that account. Keys can also be rotated on demand by logging in again.

**Key expiry**
Keys support optional expiry timestamps. Enterprise customers can request time-limited keys for contractor or auditor access.

---

## Audit Logging

Every authenticated API request generates an audit log entry containing:

| Field | Value |
|-------|-------|
| Customer ID | Account identifier |
| API key ID | Which key was used |
| Endpoint | HTTP method and path |
| Status code | Response outcome |
| Timestamp | UTC, millisecond precision |
| Source IP | Encrypted with Fernet (symmetric, AES-128-CBC) |
| User agent | Hashed (SHA-256) |
| Request ID | Unique per request (`X-Request-ID` header) |

IP addresses are encrypted, not stored in plaintext. They cannot be read without the encryption key, which is stored separately in GCP Secret Manager.

**Audit log access**
Professional and Enterprise customers can retrieve their own audit logs via the `/v1/audit-logs` endpoint. Enterprise logs are held in immutable storage (Object Lock) to prevent tampering.

---

## Encryption

| Layer | Method |
|-------|--------|
| In transit | HTTPS/TLS enforced in production (GCP Cloud Run) |
| Audit log IPs at rest | Fernet (AES-128-CBC + HMAC-SHA256) |
| API keys at rest | SHA-256 hash (one-way) |
| Database | Cloud SQL with encryption at rest (AES-256) |
| Secrets | GCP Secret Manager (no credentials in source code or environment files) |

---

## Rate Limiting

Quotas are enforced using an atomic database operation (`UPDATE ... WHERE current_usage < monthly_quota ... RETURNING`). This prevents race conditions across multiple server instances.

- Requests beyond the monthly quota return HTTP 429
- Quota resets on the 1st of each month (UTC)
- Quota and current usage are visible in the dashboard and via `/user/usage`

---

## Request Tracing

Every response includes an `X-Request-ID` header. Use this value when reporting issues to support. It links your request to the corresponding audit log entry.

---

## Error Handling

Error responses contain only a human-readable message. No internal stack traces, file paths, database queries, or system details are returned to the caller.

```json
{ "error": "Invalid API key" }
```

---

## Application Security

- No raw SQL queries. All database access uses SQLAlchemy ORM with parameterised queries (SQL injection is not possible).
- CORS restricted to allowed origins (not wildcard `*`).
- All secrets loaded from GCP Secret Manager at runtime. No credentials in source code, Docker images, or CI/CD configuration.
- Stripe webhook signatures verified before processing any billing event.

---

## SLA Commitments

| Tier | Uptime SLA | Response time (p99) | Support response |
|------|-----------|---------------------|-----------------|
| Free | 95% | Best effort | Community |
| Professional | 99.5% | Under 500ms | 24-hour email |
| Enterprise | 99.9% | Under 500ms | 4-hour phone + email |

SLA credits are available for Professional and Enterprise customers if uptime commitments are not met. Contact support for the SLA agreement template.

---

## Compliance Roadmap

| Item | Status |
|------|--------|
| HTTPS enforced | Live |
| Audit logging | Live |
| GCP encryption at rest | Live |
| SOC 2 Type II | In progress (target Q4 2026) |
| ISO 27001 | Planned (2027) |
| Australian data residency (Sydney region) | Planned (Phase 2) |

---

## Reporting a Vulnerability

If you discover a security issue, please email [security@techcompass.com.au](mailto:security@techcompass.com.au) with a description. Do not file a public GitHub issue for security vulnerabilities. We aim to acknowledge reports within 24 hours and resolve confirmed issues within 14 days.
