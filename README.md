# TechCompass API Gateway

**Compliance-first API platform for regulated finance.**

Governed access to SEC EDGAR and Federal Reserve data. Built for compliance teams.

[Sign Up Free](https://api-gateway-qyyt3rznka-uc.a.run.app/ui/signup.html) | [Quick Start](docs/QUICKSTART.md) | [API Reference](docs/API_REFERENCE.md) | [Pricing](docs/PRICING.md)

---

## Why TechCompass

Compliance and risk teams need regulatory data they can trust, audit, and defend. TechCompass provides:

**Immutable Audit Trail**
Every API call is logged with customer ID, endpoint, timestamp, status code, and encrypted source IP. Exportable for auditors on demand.

**Aggregated Regulatory APIs**
SEC EDGAR filings and Federal Reserve economic data in a single, consistent interface. No scraping. No parsing. No upstream reliability risk.

**SLA Guarantees**
99.5% uptime (Professional tier). 99.9% (Enterprise). Written commitments, not marketing promises.

**Compliance-Ready Architecture**
SHA-256 hashed API keys. Fernet-encrypted audit logs. No PII in application logs. Request tracing on every response.

---

## Data Sources

| Source | Coverage |
|--------|----------|
| SEC EDGAR | Company facts, filings (10-K, 10-Q, 8-K), submissions, full-text search |
| Federal Reserve FRED | 200,000+ economic series: GDP, CPI, interest rates, employment, housing |

---

## Get Started in 5 Minutes

1. [Create a free account](https://api-gateway-qyyt3rznka-uc.a.run.app/ui/signup.html)
2. Copy your API key from the signup confirmation
3. Make your first request:

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/facts
```

See the [Quick Start Guide](docs/QUICKSTART.md) for a full walkthrough.

---

## Documentation

| Document | Description |
|----------|-------------|
| [Quick Start](docs/QUICKSTART.md) | Get up and running in 5 minutes |
| [API Reference](docs/API_REFERENCE.md) | All endpoints, parameters, and response formats |
| [Pricing](docs/PRICING.md) | Free, Professional, and Enterprise tiers |
| [Roadmap](docs/ROADMAP.md) | Planned features and expansion timeline |
| [Security](docs/SECURITY.md) | Encryption, audit logging, SLA commitments |
| [Compliance](docs/COMPLIANCE.md) | For compliance officers and auditors |
| [FAQ](docs/FAQ.md) | Common questions |

### Code Examples

| Example | Description |
|---------|-------------|
| [curl Requests](docs/EXAMPLES/curl-requests.md) | Copy-paste curl commands for all endpoints |
| [Python Client](docs/EXAMPLES/python-client.md) | Python wrapper class with error handling |
| [Compliance Workflow](docs/EXAMPLES/compliance-workflow.md) | End-to-end counterparty screening example |

---

## Tiers at a Glance

| | Free | Professional | Enterprise |
|-|------|-------------|------------|
| Monthly calls | 50,000 | 500,000 | Unlimited |
| Price | $0 | $1,500/mo | Custom |
| Audit logs | No | Full | Immutable |
| SLA | 95% | 99.5% | 99.9% |
| Support | Community | Email | Phone |

[See full pricing details](docs/PRICING.md)

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for release history.

---

Built by [TechCompass](https://www.techcompass.com.au)
