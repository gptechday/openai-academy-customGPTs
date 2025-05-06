
# Custom GPTs — Resource Pack for workshop
*(curated links, code snippets & quick‑reference tables)*  

---

## 1  Official documentation 🔗  

| Topic | Quick link | Why it matters |
|-------|-----------|----------------|
| **GPT Actions** | [OpenAI → Actions Intro](https://platform.openai.com/docs/actions/introduction) | Register tools / back‑end APIs for your Custom GPT |
| **Data‑retrieval Actions** | [OpenAI → Data Retrieval](https://platform.openai.com/docs/actions/data-retrieval) | Enable RAG pipelines with external document stores |
| **OpenAPI Manifest Guide** | [OpenAI → Create Action](https://platform.openai.com/docs/actions/creating-action) | JSON/YAML schema that describes each action endpoint |

---

## 2  Security & guardrails 🌐  

| Resource | Focus area | Usage |
|----------|------------|-------------|
| [**OWASP Top 10 for LLM Apps (2025)**](https://owasp.org/www-project-top-10-for-large-language-model-applications/) | Critical LLM risks | Top‑10 table & mitigation map |
| [**Guardrails‑AI**](https://github.com/guardrails-ai/guardrails) | Validate & censor LLM I/O | Example PII validator |
| [**Rebuff**](https://github.com/protectai/rebuff) | Prompt‑injection detection | “Plug‑and‑play” self‑hardening wrapper |
| [**OWASP LLM cheat‑sheet (PDF)**](https://genai.owasp.org/resource/ai-security-solution-cheat-sheet-q1-2025/) | One‑page mitigations | Security best practices |
| [**DeepSeek jailbreak case‑study – WIRED**](https://www.wired.com/story/deepseeks-ai-jailbreak-prompt-injection-attacks/) | Real‑world failure | Red‑teaming anecdote |

---

## 3  Open‑source libraries & SDKs 🛠️  

| Library | Install | Capability |
|---------|---------|------------|
| **LangChain** | `pip install langchain` | Orchestrate RAG flows & tools |
| **LlamaIndex** | `pip install llama-index` | Document loaders + vector stores |
| **Guardrails‑AI** | `pip install guardrails-ai` | Declarative guards |
| **OpenAI Python** | `pip install openai` | Core SDK + Actions |
| **Rebuff** | `pip install rebuff` | Prompt‑injection wrapper |

---

## 4  Code snippet — Adding Guardrails  

```python
from guardrails import Guard
from guardrails.hub import DetectPII
import openai

prompt_guard = Guard().use(DetectPII(on_fail="exception"))

def safe_completion(user_input: str):
    prompt_guard.validate(user_input)  # 🔴 Validation before GPT
    response = openai.ChatCompletion.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": user_input}],
    )
    return response.choices[0].message.content  # 🟢 Return safe text
```

---

## 5  Sample Action Manifest (excerpt)  

```yaml
openapi: 3.1.0
info:
  title: Calendar Assistant API
  version: "1.0.0"

paths:
  /create_event:
    post:
      operationId: createEvent
      summary: Create a Google Calendar event via proxy
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Event"
      responses:
        "200":
          description: Event created

components:
  schemas:
    Event:
      type: object
      required: [title, start_time, end_time, attendees]
      properties:
        title:       { type: string }
        start_time:  { type: string, format: date-time }
        end_time:    { type: string, format: date-time }
        attendees:
          type: array
          items: { type: string, format: email }
```

---

## 6  Evaluation & red‑teaming checklist ✅  

1. **Static scans** – OWASP risk scanner on the manifest.  
2. **Dynamic tests** – fuzz with Rebuff & hostile‑prompt corpus.  
3. **Simulated tool abuse** – attempt your own function hijack.  
4. **Audit logs** – ensure every action call has trace‑ID & user‑ID.  
5. **Regressions** – rerun tests after each model update.  

---

## 7  Further reading & courses 🎓  

* [OpenAI Cookbook — GPTs & Actions](https://github.com/openai/openai-cookbook)  
* Stanford CS25 Lecture 4 — *LLM Systems & Safety* (YouTube)  
* Coursera “Building Secure AI Apps” specialization  
* [NIST IR 8472 — Adversarial Testing of AI Systems](https://doi.org/10.6028/NIST.IR.8472)  

---

---

© 2025 Muntaser Syed — MIT‑licensed for workshop attendees.
