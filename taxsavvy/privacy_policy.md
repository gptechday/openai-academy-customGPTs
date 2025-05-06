# Tax Savvy Privacy Policy
_Last updated: May 05, 2025_

Thank you for using **Tax Savvy** (“the Service”). This policy explains how **Muntaser Syed** collects, uses, and protects information when you interact with the Service.  
Questions? Email **muntaser@ieee.org**.

## 1. Data We Collect

| Category | Examples | Purpose |
|----------|----------|---------|
| **User Inputs** | Tax questions, slash commands | Generate grounded answers |
| **Usage Logs** | Timestamp, tool names, vector IDs, latency | Reliability & abuse prevention |
| **Identifiers** | Hashed OpenAI user ID, session ID | Rate‑limiting |
“Tax questions, slash commands, and uploaded tax forms (processed ephemerally).”

We **do not** collect SSNs or tax return documents.

## 2. Use of Data
- Forward queries to Pinecone index of IRS publications.
- Aggregate logs for diagnostics.
- Detect prompt‑injection attempts.

## 3. Retention
- Inputs: discarded after response.
- Logs: 30 days (usage), 90 days (errors, aggregated).

## 4. Sharing
- **Pinecone** stores embeddings in `us‑east‑1‑aws`.
- **OpenAI** processes inputs under its privacy terms.
- No advertising or analytics sharing.

## 5. Security
- TLS 1.2+ encryption.
- Encrypted log storage.
- 2‑factor admin access.

## 6. Choices
Email **muntaser@ieee.org** to request log deletion.

_By using Tax Savvy you agree to this policy._  
© 2025 Muntaser Syed
