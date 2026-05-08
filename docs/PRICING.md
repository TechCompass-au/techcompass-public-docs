# Pricing

Simple, transparent pricing. No setup fees, no data egress charges.

---

## Tiers

| | Free | Professional | Enterprise |
|-|------|-------------|------------|
| **Monthly cost** | $0 | $1,500/mo | Custom |
| **API calls/month** | 50,000 | 500,000 | Unlimited |
| **Uptime SLA** | 95% | 99.5% | 99.9% |
| **Audit logs** | No | Full | Immutable (Object Lock) |
| **Log retention** | N/A | 12 months | Custom |
| **Support** | Community (GitHub) | Email | Phone + email |
| **Response time SLA** | None | 24-hour | 4-hour |
| **Compliance docs** | Basic | Standard | Premium |
| **Data lineage** | No | Yes | Yes |
| **Vendor risk register** | No | On request | Included |
| **Custom integrations** | No | No | Yes |
| **Regional hosting** | US only | US + AU | Custom |
| **Annual discount** | N/A | 10% | Negotiable |

---

## What Counts as an API Call

Each HTTP request to a data endpoint counts as one call. This includes:

- `/v1/sec/company/{cik}/facts`
- `/v1/sec/company/{cik}/submissions`
- `/v1/sec/search`
- `/v1/fred/series/{series_id}/data`
- `/v1/fred/search`

The following do **not** count toward your quota:

- `/auth/signup`
- `/auth/login`
- `/user/profile`
- `/user/usage`
- `/health`

---

## Upgrade to Professional

Upgrade from the dashboard at any time:

[https://api-gateway-qyyt3rznka-uc.a.run.app/ui/dashboard.html](https://api-gateway-qyyt3rznka-uc.a.run.app/ui/dashboard.html)

Your quota is upgraded immediately after payment. No waiting for the next billing cycle.

---

## Enterprise Enquiries

Enterprise tier includes unlimited calls, immutable audit logs suitable for regulatory submission, custom regional hosting, SLA agreements, and dedicated support.

Contact us at [hello@techcompass.com.au](mailto:hello@techcompass.com.au) to discuss requirements.

---

## Frequently Asked Questions

**Can I upgrade or downgrade?**
You can upgrade at any time via the dashboard. Downgrade requests are handled at the end of your current billing period. Contact support to initiate a downgrade.

**What happens when I reach my monthly limit?**
Requests return HTTP 429 (quota exceeded). Your existing integration continues working the following month when the quota resets on the 1st. You will not be automatically charged overage fees on the Free tier.

**Do you offer a trial of Professional features?**
The Free tier is available indefinitely with no credit card required. If you need to evaluate Professional features for a procurement process, contact us to arrange a short-term trial.

**Is there an annual billing option?**
Yes. Annual billing for the Professional tier is available at a 10% discount. Contact [hello@techcompass.com.au](mailto:hello@techcompass.com.au).

**What payment methods do you accept?**
Credit and debit cards via Stripe. Invoice billing is available for Enterprise customers.

**Are there any setup fees?**
No. Free accounts are available immediately. Professional activation happens within minutes of payment.
