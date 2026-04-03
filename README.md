# n8n PII Extraction — Form Upload

An [n8n](https://n8n.io) workflow that accepts any document upload (PDF, DOCX, CSV, TXT) via a web form, runs it through Claude on AWS Bedrock, and returns a structured CSV cataloguing every PII instance found — with sensitivity classification and confidence scores.

---

## ⚠️ GDPR Notice

This workflow is a PII **detection tool**. Before deploying:

- Only scan documents for which you have a **legitimate legal basis** under GDPR Art. 6.
- Apply **data minimisation** — do not feed documents containing more personal data than necessary for the scan.
- The CSV output contains real extracted PII values. **Delete it once your review is complete.** Do not retain PII output beyond the purpose for which it was collected.
- Disable n8n execution log saving for this workflow (**Workflow Settings → Save Execution Progress → Never**) to avoid persisting personal data in logs.
- If your n8n instance or AWS region is outside the EU/EEA, ensure a **Standard Contractual Clause (SCC)** or equivalent is in place with AWS.
- Restrict access to this workflow to authorised personnel only using n8n's user roles.

---

## How It Works

```
Form Trigger → Extract from File → AI Agent (Claude via AWS Bedrock) → Parse Output → Convert to CSV
```

1. User uploads a document through a web form
2. n8n extracts the text content
3. Claude analyses the full text and identifies every PII instance
4. Output is parsed and structured into a downloadable CSV

---

## Output

A CSV file (`pii_results.csv`) with one row per PII instance found:

| Column | Description |
|--------|-------------|
| `id` | Sequential row number |
| `field_name` | PII type in snake_case (e.g. `full_name`, `email`, `ssn`) |
| `value` | Exact extracted value as it appears in the source |
| `sensitivity` | `HIGH`, `MEDIUM`, or `LOW` |
| `confidence` | Float 0.0–1.0 |
| `location` | Line number or position in source |
| `context` | ~20 characters of surrounding text |

**Sensitivity levels:**
- 🔴 **HIGH** — SSN, passport, credit card, bank account, medical record
- 🟡 **MEDIUM** — Full name, email, phone, date of birth, home address
- 🟢 **LOW** — First name alone, job title linked to name, city/state

**Compliance flags** are automatically generated per scan: GDPR, CCPA, HIPAA — based on PII types found.

---

## Prerequisites

- n8n v1.88+
- AWS account with Bedrock access and a Claude model enabled
- IAM user/role with `bedrock:InvokeModel` permission

---

## Setup

See **[docs/setup.md](docs/setup.md)** for full configuration steps.

Quick summary:
1. Import `workflows/pii_extraction.json` into n8n
2. Create an **AWS Bedrock** credential in n8n and assign it to all Bedrock nodes
3. Set your model ID to match what's enabled in your AWS region
4. Activate and open the Form Trigger URL

---

## Tech Stack

| Tool | Role |
|------|------|
| n8n | Workflow orchestration |
| AWS Bedrock | Claude model hosting |
| Claude Sonnet | PII detection agent |

---

## License

MIT
