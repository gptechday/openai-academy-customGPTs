# Prompt‑Engineering Micro‑Patterns – Addendum

## 1. Guardrail Sandwich

Layered prompt structure that puts **non‑negotiable safety / compliance rules** on the outside *(System)* and **stylistic or task‑specific guidance** inside *(Developer)*, with one or more few‑shot examples sandwiched between.

| Layer | Example | Purpose |
|-------|---------|---------|
| **System** | You are **Tax Savvy**, an assistant that must comply with U.S. IRS regulations and never provide legal or financial advice beyond publicly available IRS publications. | Hard safety & scope |
| **Developer** | Within those IRS constraints, answer conversationally in plain English, using bullet lists when enumerating deductions. | Style & flexibility |
| **Few‑shot** | **User:** “What’s the student‑loan interest cap for 2024?”  <br> **Assistant:** “Up to $2,500 may be deductible, subject to income phase‑outs.” | Reference answer |

---

## 2. Reference Tags to Scope Answers

Fence source text with custom tags so the model only cites the provided passages.

```text
<System>
You have access to the following source:
<Pub970>
You may be able to deduct up to $2,500 of student‑loan interest...
</Pub970>
Always cite the paragraph ID when you quote.
</System>

<User>
Can I deduct my student‑loan interest this year?
```

---

## 3. Role‑play Personas

| Persona | Directive Snippet | Intended Outcome |
|---------|-------------------|------------------|
| **Socratic tutor** | Act as a Socratic tutor: never state the full answer immediately; instead, ask the learner guiding questions until they reach the conclusion. | Promotes critical thinking |
| **Code reviewer** | Play a strict senior engineer reviewing pull requests. Point out security issues first, then style nits. | Generates actionable PR feedback |
| **Product manager** | Respond as a PM prioritizing features using the MoSCoW method. Output a table with Must, Should, Could, Won’t. | Produces structured prioritization |

---

## 4. Slash‑Command Temperature Toggles

Expose creativity controls to users via simple slash commands parsed at the start of each message.

```text
Developer instruction snippet:

Recognize the following slash commands:
/creative → temperature 0.9, top_p 1.0
/strict   → temperature 0.2, top_p 0.9

If no slash is provided, default to temperature 0.5.

Few‑shot:

User: /creative Rewrite “Company profits decline”
Assistant: “Storm Clouds or Silver Linings? A Look Behind Falling Profits”

User: /strict Summarize "To Kill a Mockingbird" in one sentence
Assistant: (concise factual summary)
```

---

## Quick Copy Templates

```text
# Guardrail sandwich
<System>
You are ___ (role). Mandatory rules:
1. ___
2. ___
</System>
<Developer>
Within those rules, please ___ (tone, format).
</Developer>

# Reference tags
<SourceA>
...authoritative passage...
</SourceA>
<SourceB>
...another passage...
</SourceB>

# Role‑play
<Developer>
Act as a ___ (persona). Key behaviors:
- ...
- ...
</Developer>

# Slash‑command toggle
/creative → temperature 0.9
/strict   → temperature 0.2
```
