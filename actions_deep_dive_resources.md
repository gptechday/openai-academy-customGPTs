
# Actions Deep Dive — Resource Pack

This file complements the “Actions Deep Dive” slide by giving copy‑ready links, code, and checklists.

---

## 1  YAML manifest — schema • auth • error_map

| What you need | Quick link | Snippet |
|---------------|------------|---------|
| **Full manifest spec** | [OpenAI → Actions intro](https://platform.openai.com/docs/actions/introduction) | — |
| **Auth options** | [OpenAI → Authentication](https://platform.openai.com/docs/actions/authentication) | ```yaml
components:
  securitySchemes:
    api_key:
      type: apiKey
      in: header
      name: X-Api-Key
security:
  - api_key: []
``` |
| **Error map reference** | [API error codes](https://platform.openai.com/docs/guides/error-codes/api-errors) | ```yaml
x-openai-error-map:
  400:
    code: BAD_REQUEST
    message: "Check parameters & schema."
  504:
    code: UPSTREAM_TIMEOUT
    message: "Service took too long — retry later."
``` |

---

## 2  Performance — stay under 45 s

* OpenAI enforces **45 s round‑trip** timeout.  
* Aim for **< 5 s** typical latency; cache or pre‑compute heavy calls.  
* For slow tasks, return `202 Accepted` and let GPT poll `/status/{job_id}`.

```python
import requests, backoff
SESSION = requests.Session()

@backoff.on_exception(backoff.expo, (requests.Timeout, requests.HTTPError), max_time=30)
def fast_call(payload: dict):
    r = SESSION.post(
        "https://api.acme.dev/v1/quick_lookup",
        json=payload,
        timeout=5
    )
    r.raise_for_status()
    return r.json()
```

---

## 3  Graceful fallbacks

| Pattern | Use‑case | Hint |
|---------|----------|------|
| **Retry w/ back‑off** | 5xx, 429 | `backoff` (Python) / `axios-retry` (JS) |
| **Default answer** | Non‑critical lookup fails | Return last‑known value or cached stub |
| **User hint** | After max retries | GPT: “Service is busy, try again later.” |

---

## 4  Structured errors

```jsonc
{
  "code": "BAD_REQUEST",
  "message": "Parameter `ticker` must match ^[A-Z]{1,5}$"
}
```

*GPT can paraphrase `message` while analytics rely on stable `code`.*

---

## 5  Red‑team & QA checklist

1. **Lint** manifest (`speccy lint`).  
2. Hit endpoints **without auth** → expect 401.  
3. **Delay ≥ 45 s** → confirm timeout hint.  
4. Inject invalid field → expect `BAD_REQUEST`.  
5. **Rate‑limit** to 1 r/s → ensure back‑off activates.

---

© 2025 Muntaser Syed — MIT‑licensed
