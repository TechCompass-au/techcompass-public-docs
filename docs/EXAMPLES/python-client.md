# Python Client

A lightweight Python client for the TechCompass API Gateway.

**Requirements:** Python 3.8+, `requests` library

```bash
pip install requests
```

---

## Client Class

```python
import requests


class TechCompassClient:
    """TechCompass API Gateway client."""

    BASE_URL = "https://api-gateway-qyyt3rznka-uc.a.run.app"

    def __init__(self, api_key: str):
        self.api_key = api_key
        self.session = requests.Session()
        self.session.headers.update({"Authorization": f"ApiKey {api_key}"})

    # ── Auth ──────────────────────────────────────────────────────────────

    @classmethod
    def signup(cls, email: str, password: str) -> dict:
        """Create a new account. Returns api_key, user_id, tier, quota."""
        res = requests.post(
            f"{cls.BASE_URL}/auth/signup",
            json={"email": email, "password": password},
        )
        res.raise_for_status()
        return res.json()

    @classmethod
    def login(cls, email: str, password: str) -> "TechCompassClient":
        """Log in and return a configured client. Rotates the API key."""
        res = requests.post(
            f"{cls.BASE_URL}/auth/login",
            json={"email": email, "password": password},
        )
        res.raise_for_status()
        data = res.json()
        print(f"New API key issued: {data['api_key'][:20]}...")
        return cls(api_key=data["api_key"])

    # ── User ──────────────────────────────────────────────────────────────

    def get_usage(self) -> dict:
        """Return usage stats for the current billing month."""
        res = self.session.get(f"{self.BASE_URL}/user/usage")
        res.raise_for_status()
        return res.json()

    def get_profile(self) -> dict:
        res = self.session.get(f"{self.BASE_URL}/user/profile")
        res.raise_for_status()
        return res.json()

    # ── SEC EDGAR ────────────────────────────────────────────────────────

    def get_company_facts(self, cik: str) -> dict:
        """Fetch all XBRL financial facts for a company by CIK."""
        res = self.session.get(f"{self.BASE_URL}/v1/sec/company/{cik}/facts")
        if res.status_code == 404:
            raise ValueError(f"Company not found: CIK {cik}")
        res.raise_for_status()
        return res.json()

    def get_company_submissions(self, cik: str) -> dict:
        """Fetch filing history for a company by CIK."""
        res = self.session.get(f"{self.BASE_URL}/v1/sec/company/{cik}/submissions")
        if res.status_code == 404:
            raise ValueError(f"Company not found: CIK {cik}")
        res.raise_for_status()
        return res.json()

    def search_sec(self, query: str, limit: int = 10) -> list:
        """Full-text search across SEC EDGAR filings."""
        res = self.session.get(
            f"{self.BASE_URL}/v1/sec/search",
            params={"q": query, "limit": limit},
        )
        res.raise_for_status()
        return res.json()

    # ── Federal Reserve (FRED) ────────────────────────────────────────────

    def get_fred_series(self, series_id: str) -> dict:
        """Fetch observations for a FRED economic data series."""
        res = self.session.get(f"{self.BASE_URL}/v1/fred/series/{series_id}/data")
        if res.status_code == 404:
            raise ValueError(f"FRED series not found: {series_id}")
        res.raise_for_status()
        return res.json()

    def search_fred(self, query: str, limit: int = 10) -> list:
        """Search the FRED series catalog."""
        res = self.session.get(
            f"{self.BASE_URL}/v1/fred/search",
            params={"q": query, "limit": limit},
        )
        res.raise_for_status()
        return res.json()
```

---

## Usage Examples

### Create an Account

```python
result = TechCompassClient.signup(
    email="analyst@yourfirm.com",
    password="your-secure-password"
)

print(f"User ID:  {result['user_id']}")
print(f"Tier:     {result['tier']}")
print(f"Quota:    {result['quota']:,} calls/month")
print(f"API Key:  {result['api_key']}")
# Save api_key securely — it is shown only once
```

### Look Up Apple's SEC Data

```python
client = TechCompassClient(api_key="sk_live_YOUR_KEY")

# Apple Inc. CIK: 320193
facts = client.get_company_facts("320193")

# Print entity name and available concept namespaces
print(facts["entityName"])           # Apple Inc.
print(list(facts["facts"].keys()))   # ['us-gaap', 'dei', 'ifrs-full', ...]
```

### Retrieve Revenue Data

```python
client = TechCompassClient(api_key="sk_live_YOUR_KEY")

facts = client.get_company_facts("320193")
revenue = facts["facts"]["us-gaap"]["Revenues"]["units"]["USD"]

# Most recent 5 annual filings
annual = [r for r in revenue if r.get("form") == "10-K"]
for entry in sorted(annual, key=lambda x: x["end"])[-5:]:
    print(f"{entry['end']}: ${entry['val']:,.0f}")
```

### Fetch Federal Reserve GDP Data

```python
client = TechCompassClient(api_key="sk_live_YOUR_KEY")

gdp = client.get_fred_series("GDP")
observations = gdp["observations"]

# Print most recent 4 quarters
for obs in observations[-4:]:
    print(f"{obs['date']}: ${float(obs['value']):,.1f}B")
```

### Check Monthly Usage

```python
client = TechCompassClient(api_key="sk_live_YOUR_KEY")

usage = client.get_usage()
print(f"Calls used:  {usage['calls_this_month']:,}")
print(f"Quota:       {usage['quota_limit']:,}")
print(f"Percent:     {usage['percent_used']}%")
print(f"Resets:      {usage['reset_date']}")
```

---

## Error Handling

```python
import requests

client = TechCompassClient(api_key="sk_live_YOUR_KEY")

try:
    facts = client.get_company_facts("999999999")
except ValueError as e:
    print(f"Not found: {e}")
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 429:
        print("Monthly quota exceeded. Upgrade at /ui/dashboard.html")
    elif e.response.status_code == 401:
        print("Invalid API key. Log in again to get a fresh key.")
    else:
        print(f"API error {e.response.status_code}: {e.response.json()}")
```
