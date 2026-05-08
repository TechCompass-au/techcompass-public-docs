# Quick Start Guide

Get from zero to your first API response in under 5 minutes.

---

## Prerequisites

You need `curl` installed. It comes pre-installed on macOS and most Linux distributions.

**Windows:** Install via [curl.se/windows](https://curl.se/windows) or use Git Bash / WSL.

---

## Step 1: Create Your Account

Go to the signup page and enter your email and password:

[https://api-gateway-qyyt3rznka-uc.a.run.app/ui/signup.html](https://api-gateway-qyyt3rznka-uc.a.run.app/ui/signup.html)

Or use the API directly:

```bash
curl -X POST https://api-gateway-qyyt3rznka-uc.a.run.app/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email": "you@yourcompany.com", "password": "your-secure-password"}'
```

**Response:**

```json
{
  "user_id": "a1b2c3d4-...",
  "api_key": "<your-api-key>",
  "tier": "free",
  "quota": 50000
}
```

> **Important:** Copy your API key now. It is displayed only once. If you lose it, log in again to generate a new one.

---

## Step 2: Save Your API Key

Store the key somewhere secure (a password manager or secrets vault). You will include it in every API request as an `Authorization` header:

```
Authorization: ApiKey sk_live_YOUR_KEY_HERE
```

---

## Step 3: Make Your First Request

Fetch SEC EDGAR company facts for Apple Inc. (CIK: 320193):

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY_HERE" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/sec/company/320193/facts
```

You will receive a JSON response with Apple's reported financial facts directly from SEC EDGAR.

**Try a Federal Reserve data series (US GDP):**

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY_HERE" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/v1/fred/series/GDP/data
```

---

## Step 4: Check Your Usage Dashboard

View your API call count, quota, and reset date in the dashboard:

[https://api-gateway-qyyt3rznka-uc.a.run.app/ui/dashboard.html](https://api-gateway-qyyt3rznka-uc.a.run.app/ui/dashboard.html)

Or check programmatically:

```bash
curl -H "Authorization: ApiKey sk_live_YOUR_KEY_HERE" \
  https://api-gateway-qyyt3rznka-uc.a.run.app/user/usage
```

**Response:**

```json
{
  "calls_this_month": 3,
  "quota_limit": 50000,
  "percent_used": 0.0,
  "reset_date": "2026-06-01",
  "tier": "free"
}
```

---

## Authentication Reference

All authenticated endpoints require this header:

```
Authorization: ApiKey sk_live_YOUR_KEY_HERE
```

Public endpoints (signup, login, health check) do not require authentication.

---

## Error Handling

| Status | Meaning |
|--------|---------|
| `200` | Success |
| `400` | Bad request (check your JSON body) |
| `401` | Missing or invalid API key |
| `403` | Valid key, endpoint requires user account |
| `404` | Resource not found (e.g. unknown CIK) |
| `429` | Monthly quota exceeded (upgrade or wait for reset) |
| `500` | Server error (contact support) |
| `502` | Upstream service temporarily unavailable |
| `503` | Service temporarily unavailable |

Error responses follow this format:

```json
{
  "error": "Monthly quota exceeded",
  "quota": 50000,
  "tier": "free",
  "upgrade_url": "/billing/upgrade-to-pro"
}
```

---

## Next Steps

- [API Reference](API_REFERENCE.md) — full endpoint documentation
- [Examples: curl](EXAMPLES/curl-requests.md) — copy-paste request library
- [Examples: Python](EXAMPLES/python-client.md) — Python client class
- [Pricing](PRICING.md) — upgrade to Professional for higher quotas and SLA guarantees
