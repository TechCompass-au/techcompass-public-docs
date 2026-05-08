# Compliance Workflow: Counterparty Screening

This example walks through a realistic compliance use case: a risk team needs to screen a counterparty before entering a transaction, verify data access is logged, and produce an audit trail.

---

## Scenario

**Team:** Credit risk at an investment manager
**Task:** Screen a counterparty (a US public company) for financial health indicators before approving a credit facility
**Requirement:** All data access must be auditable with timestamps

---

## Step 1: Look Up the Counterparty in SEC EDGAR

You need the company's SEC CIK. For Apple Inc. it is `320193`. You can search EDGAR's company search at [efts.sec.gov](https://efts.sec.gov) or use our search endpoint.

**Search by name:**

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/search?q=Apple+Inc&limit=5"
```

**Retrieve company facts (financial data):**

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/facts \
  -o apple_facts.json
```

**Retrieve filing history:**

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/submissions \
  -o apple_submissions.json
```

---

## Step 2: Extract Key Financial Indicators (Python)

```python
import json

with open("apple_facts.json") as f:
    facts = json.load(f)

gaap = facts["facts"]["us-gaap"]

# Revenue — last 3 annual periods
revenues = gaap.get("Revenues", {}).get("units", {}).get("USD", [])
annual_rev = [r for r in revenues if r.get("form") == "10-K"]
print("Annual Revenue (most recent 3):")
for r in sorted(annual_rev, key=lambda x: x["end"])[-3:]:
    print(f"  {r['end']}: ${r['val']:,.0f}")

# Total Liabilities
liabilities = gaap.get("Liabilities", {}).get("units", {}).get("USD", [])
annual_liab = [l for l in liabilities if l.get("form") == "10-K"]
print("\nTotal Liabilities (most recent):")
latest_liab = sorted(annual_liab, key=lambda x: x["end"])[-1]
print(f"  {latest_liab['end']}: ${latest_liab['val']:,.0f}")
```

**Sample output:**

```
Annual Revenue (most recent 3):
  2023-09-30: $383,285,000,000
  2024-09-30: $391,035,000,000
  2025-09-30: $394,328,000,000

Total Liabilities (most recent):
  2025-09-30: $308,927,000,000
```

---

## Step 3: Check Economic Context (Federal Reserve Data)

Before finalising the credit assessment, check the prevailing interest rate environment:

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/FEDFUNDS/data \
  -o fed_funds.json
```

```python
import json

with open("fed_funds.json") as f:
    data = json.load(f)

observations = data["observations"]
recent = sorted(observations, key=lambda x: x["date"])[-3:]
print("Federal Funds Rate (recent):")
for obs in recent:
    print(f"  {obs['date']}: {obs['value']}%")
```

---

## Step 4: Retrieve Your Audit Log

After completing the data pulls, export your audit log to confirm all accesses are recorded:

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY" \
  "https://api-gateway-qyyt3rznka-uc.a.run.app/v1/audit-logs?start_date=2026-05-08&end_date=2026-05-08"
```

**Sample audit log response:**

```json
[
  {
    "timestamp": "2026-05-08T03:41:02.101Z",
    "customer_id": "credit-team-acme",
    "api_key_id": 7,
    "endpoint": "/v1/sec/search",
    "method": "GET",
    "status_code": 200,
    "request_id": "req_3hKm9wPqNz"
  },
  {
    "timestamp": "2026-05-08T03:41:18.442Z",
    "customer_id": "credit-team-acme",
    "api_key_id": 7,
    "endpoint": "/v1/sec/company/320193/facts",
    "method": "GET",
    "status_code": 200,
    "request_id": "req_7bYw4jLzTs"
  },
  {
    "timestamp": "2026-05-08T03:41:39.887Z",
    "customer_id": "credit-team-acme",
    "api_key_id": 7,
    "endpoint": "/v1/sec/company/320193/submissions",
    "method": "GET",
    "status_code": 200,
    "request_id": "req_2mXp5vNkQr"
  },
  {
    "timestamp": "2026-05-08T03:42:11.204Z",
    "customer_id": "credit-team-acme",
    "api_key_id": 7,
    "endpoint": "/v1/fred/series/FEDFUNDS/data",
    "method": "GET",
    "status_code": 200,
    "request_id": "req_9kXp2mNvQr"
  }
]
```

---

## Step 5: Provide Audit Trail to Auditor

The audit log shows:
- **Who** accessed the data (customer ID and API key ID)
- **What** was accessed (exact endpoint and HTTP method)
- **When** it was accessed (UTC timestamp)
- **Whether it succeeded** (status code 200)

Each `request_id` can be cross-referenced with server-side infrastructure logs if deeper verification is required.

Save the JSON export as part of your credit file. Enterprise customers can also retrieve an immutable version held in Object Lock storage that cannot be altered after the fact.

---

## Interpretation Notes

| SEC Concept | GAAP Tag | Notes |
|-------------|----------|-------|
| Revenue | `Revenues` or `RevenueFromContractWithCustomerExcludingAssessedTax` | Tag varies by company |
| Net income | `NetIncomeLoss` | |
| Total assets | `Assets` | |
| Total liabilities | `Liabilities` | |
| Long-term debt | `LongTermDebt` | |
| Cash | `CashAndCashEquivalentsAtCarryingValue` | |

Annual filings: `form == "10-K"` | Quarterly: `form == "10-Q"`

---

## Further Reading

- [API Reference](../API_REFERENCE.md) — full endpoint documentation
- [Compliance Guide](../COMPLIANCE.md) — audit log export, data lineage, vendor risk
- [Security](../SECURITY.md) — encryption and access controls
