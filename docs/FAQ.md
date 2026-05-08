# Frequently Asked Questions

---

## Data

**How fresh is the SEC data?**
SEC EDGAR data is fetched from the official EDGAR API on demand and cached for up to 1 hour. For most use cases (company filings, financial facts, submission history), hourly freshness is more than sufficient. EDGAR itself publishes new filings within minutes of acceptance — same-day data is available if your cache TTL has expired.

**How fresh is the FRED data?**
Federal Reserve FRED series are fetched on demand and cached. FRED updates series on varying schedules — GDP quarterly, CPI monthly, FEDFUNDS daily. TechCompass passes through the latest available values from FRED at time of request.

**Do you modify or transform the data?**
No. Values returned by TechCompass match the values published by the source agency (SEC EDGAR or FRED). We do not normalise, adjust, or derive new values. This supports data lineage requirements.

**What CIK format should I use?**
SEC Central Index Keys can be submitted with or without leading zeros. Both `320193` and `0000320193` resolve to Apple Inc.

---

## Hosting and Regions

**Where is data hosted?**
Currently in Google Cloud (us-central1). Australian regional hosting (ap-southeast-2, Sydney) is planned for Phase 2. See [ROADMAP.md](ROADMAP.md).

**Can I request AU data residency today?**
If AU data residency is a hard requirement, contact us at [hello@techcompass.com.au](mailto:hello@techcompass.com.au). Enterprise customers can be prioritised for early Sydney-region access.

---

## Compliance and Audit

**Do you provide audit logs?**
Yes. Professional and Enterprise tiers include full audit logs covering every API request made by your account. Logs are accessible via the API and exportable as JSON.

**Can our auditors access the logs?**
Yes. Audit logs are exportable and can be provided directly to auditors. Enterprise logs are stored with Object Lock (immutable) so auditors can trust they have not been modified.

**Do you offer a data processing agreement (DPA)?**
Yes, for Enterprise customers. Contact [compliance@techcompass.com.au](mailto:compliance@techcompass.com.au).

**Is TechCompass SOC 2 certified?**
SOC 2 Type II is in progress with a target of Q4 2026. In the meantime, we can provide a security overview, architecture documentation, and penetration test summary (Enterprise, under NDA).

**What is your data retention policy?**
Audit logs are retained for 12 months on Professional and for a custom period on Enterprise. Free tier does not include audit log retention.

---

## Pricing and Billing

**Can I trial for free?**
Yes. The Free tier (50,000 calls/month) is available indefinitely with no credit card required. Sign up at [the signup page](https://api-gateway-qyyt3rznka-uc.a.run.app/ui/signup.html).

**What is your SLA?**
- Free: 95% uptime
- Professional: 99.5% uptime, 24-hour email support
- Enterprise: 99.9% uptime, 4-hour phone support

**What happens if I exceed my quota?**
Requests return HTTP 429 until the quota resets on the 1st of the following month. You will not be charged overages. Upgrade to Professional at any time to immediately increase your quota.

**Can I upgrade or downgrade?**
Upgrade is instant via the dashboard. Downgrades take effect at the end of the current billing period. Contact [hello@techcompass.com.au](mailto:hello@techcompass.com.au) to arrange.

**Do you offer annual billing?**
Yes, with a 10% discount for Professional tier. Enterprise pricing is negotiated. Contact us to arrange.

---

## Support

**What is your support response time?**
- Free: Community support via GitHub Issues
- Professional: Email support, 24-hour response SLA
- Enterprise: Phone and email, 4-hour response SLA

**How do I report a bug or data issue?**
Open a GitHub Issue on this repository, or email [support@techcompass.com.au](mailto:support@techcompass.com.au). Include your `X-Request-ID` header value if reporting an API error.

---

## Integration

**Can you integrate with Salesforce or our internal system?**
Custom integrations are available for Enterprise customers. This includes webhook delivery to your systems, custom data formats, and dedicated support for your integration team. Contact [hello@techcompass.com.au](mailto:hello@techcompass.com.au).

**Do you have a Python SDK?**
We provide a copy-paste Python client in [EXAMPLES/python-client.md](EXAMPLES/python-client.md). A formal SDK package is on the roadmap.

**Do you support webhooks?**
Filing and entity monitoring with webhook delivery is planned for Phase 5. See [ROADMAP.md](ROADMAP.md).
