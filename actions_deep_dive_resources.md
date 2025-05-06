
# Actions Deep Dive — Resource Pack

A handy companion for the **“Actions Deep Dive”** slide. Copy‑ready links, code snippets, and a mini QA checklist.

---

## 1 YAML Manifest Essentials

### 📚 Reference Links
* **Full manifest spec:** <https://platform.openai.com/docs/actions/introduction>
* **Auth schemes:** <https://platform.openai.com/docs/actions/authentication>
* **Error‑code reference:** <https://platform.openai.com/docs/guides/error-codes/api-errors>

### 🔐 Auth snippet

```yaml
components:
  securitySchemes:
    api_key:
      type: apiKey
      in: header
      name: X-Api-Key

security:
  - api_key: []
```

### 🚦 Error‑map snippet

```yaml
x-openai-error-map:
  400:
    code: BAD_REQUEST
    message: "Check parameters & schema."
  504:
    code: UPSTREAM_TIMEOUT
    message: "Upstream took too long — retry later."
```

---

## 2 Performance Budget (≤ 45 s round‑trip)

* OpenAI enforces a **45 s** timeout on each Action call.  
* Aim for **< 5 s** typical latency. Cache or pre‑compute heavy work.  
* For slow jobs, return **`202 Accepted`** with a job ID and let GPT poll `/status/{id}`.

```python
import requests, backoff

SESSION = requests.Session()  # connection pooling

@backoff.on_exception(backoff.expo,
                      (requests.Timeout, requests.HTTPError),
                      max_time=30)
def fast_call(payload: dict):
    r = SESSION.post(
        "https://api.acme.dev/v1/quick_lookup",
        json=payload,
        timeout=5          # client‑side hard limit
    )
    r.raise_for_status()
    return r.json()
```

---

## 3 Graceful Fallback Patterns

| Pattern | When to use | Implementation hint |
|---------|-------------|---------------------|
| **Retry (back‑off)** | Intermittent **5xx** or **429** | Python `backoff`, JS `axios-retry` |
| **Default answer** | Non‑critical read fails | Serve last‑known or cached stub |
| **User hint** | After max retries | GPT: *“Service is busy, try again shortly.”* |

---

## 4 Structured Error Example

```jsonc
{
  "code": "BAD_REQUEST",
  "message": "Parameter `ticker` must match ^[A-Z]{1,5}$"
}
```

*The model can paraphrase `message` while dashboards rely on stable `code` values.*

---

## 5 Red‑Team & QA Checklist ✅

1. **Lint** the manifest (`redocly lint` or `speccy lint`).  
2. Call endpoints **without auth** → expect **401**.  
3. Simulate a **≥ 45 s delay** → GPT surfaces timeout hint.  
4. Inject an invalid field → confirm **BAD_REQUEST** bubbles up.  
5. Throttle to **1 req/s** → ensure exponential back‑off kicks in.

---

© 2025 Muntaser Syed — MIT‑licensed
