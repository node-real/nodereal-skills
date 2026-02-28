# Portal API Reference — Account & Usage Management

## Overview

The Portal API provides programmatic access to MegaNode account management, CU consumption monitoring, and usage analytics. This enables developers to build dashboards, set up alerts, and track API usage without logging into the web console.

---

## Endpoint

```
https://open-platform-ap.nodereal.io/{apikey}/portal
```

---

## API Methods

### CU Consumption Query

Get your account's Compute Unit consumption data.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "nr_getCUConsumption",
  "params": [{
    "startDate": "2024-01-01",
    "endDate": "2024-01-31"
  }]
}
```

**Response:**
```json
{
  "result": {
    "totalCU": 150000000,
    "dailyUsage": [
      {
        "date": "2024-01-01",
        "cu": 5000000
      }
    ]
  }
}
```

### CU Detail Query

Get detailed CU breakdown per method and API key.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "nr_getCUDetail",
  "params": [{
    "apiKey": "your-api-key",
    "startDate": "2024-01-01",
    "endDate": "2024-01-31"
  }]
}
```

**Response:**
```json
{
  "result": {
    "methodBreakdown": [
      {
        "method": "eth_call",
        "totalCU": 50000,
        "requestCount": 2500
      },
      {
        "method": "eth_getBalance",
        "totalCU": 30000,
        "requestCount": 2000
      }
    ]
  }
}
```

---

## Use Cases

- **Usage dashboards:** Build internal monitoring for CU consumption trends
- **Cost alerts:** Trigger alerts when approaching monthly CU quota
- **Per-method analytics:** Identify which API methods consume the most CUs
- **Per-key tracking:** Monitor usage across different API keys / projects

---

## Best Practices

- Query CU consumption daily to catch usage spikes early
- Set up alerts at 50%, 75%, and 90% of monthly CU quota
- Use per-method breakdown to optimize high-CU-cost patterns
- Monitor across all API keys — CU quota is account-level, not per-key
