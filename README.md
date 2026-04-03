# n8n Prompt SQL Tester — Teradata MCP

An [n8n](https://n8n.io) chat-based testing harness for LLM prompts that generate Teradata SQL. Upload a YAML prompts file, ask a business question, and the workflow automatically runs 3 paraphrased variants through your prompt, validates the SQL, scores consistency, and delivers a CSV report with token usage and improvement recommendations.

---

## ⚠️ GDPR Notice

This workflow processes business questions entered by users and may generate SQL that references sensitive database schemas. Before deploying:

- Instruct users **not to embed real personal data** in test questions (e.g. do not use actual customer names, IDs, or account numbers as example values).
- The CSV output includes generated SQL queries which may reveal sensitive table/column names. **Review before sharing externally.**
- Disable n8n execution log saving for this workflow (**Workflow Settings → Save Execution Progress → Never**) to avoid persisting conversation history containing potentially sensitive schema information.
- If connecting to a database containing personal data, ensure a **Data Processing Agreement (DPA)** is in place with all parties involved and that access is limited to authorised testers only.

---

## How It Works

```
Chat Trigger
  → YAML Parser (reads uploaded prompts file)
  → Intake + Paraphrase Agent (collects prompt name + question, generates 3 variants)
  → Loop ×3
      → SQL Agent (runs prompt against Teradata MCP tool)
      → Token Capture
      → Validate + Accumulate (checks Teradata dialect compliance)
  → Compile Summary
  → Recommendation Agent (suggests question improvements)
  → CSV Output + Chat Response
```

---

## Features

- **3-variant testing** — automatically generates meaningfully different phrasings of your question
- **Teradata SQL validation** — checks every generated query for dialect compliance
- **Consistency scoring** — flags when different phrasings produce structurally different SQL
- **Token tracking** — estimates input/output tokens per run
- **Recommendation agent** — analyses failures and suggests improved question formulations
- **CSV export** — full run report downloadable from chat

---

## SQL Validations

| Rule | Why |
|------|-----|
| No `LIMIT` / `FETCH` / `OFFSET` | Use `SELECT TOP n` in Teradata |
| No backticks | Use double quotes for identifiers |
| No square brackets `[ ]` | SQL Server syntax, not Teradata |
| No `!=` | Use `<>` |
| No `ISNULL()` | Use `COALESCE()` |
| No `SELECT *` | Explicit columns required |
| No window functions in `WHERE` | Use `QUALIFY` clause |

---

## YAML Prompts File Format

```yaml
- prompt_name: "your_prompt_name"
  prompt_type: "sql_generation"
  prompt_description: "What this prompt does"
  prompt_content: |
    You are a SQL generation assistant for Teradata.
    [Your full system prompt here]

- prompt_name: "your_prompt_improver_name"
  prompt_type: "improvement"
  prompt_description: "Recommendation agent system prompt"
  prompt_content: |
    You are an expert at improving business questions for SQL generation.
    [Improvement prompt here]
```

> The workflow looks for a prompt named `your_prompt_improver_name` to power the recommendation step. Rename it to anything you like — just update the reference in node **7b · Extract Recommend Prompt**.

---

## Prerequisites

- n8n v1.88+
- AWS account with Bedrock access and a Claude model enabled
- Teradata MCP server running and accessible from your n8n instance
- n8n setting enabled: `N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true`

---

## Setup

See **[docs/setup.md](docs/setup.md)** for full configuration steps.

Quick summary:
1. Import `workflows/prompt_sql_tester.json` into n8n
2. Create an **AWS Bedrock** credential and assign it to all Bedrock nodes
3. Point the **MCP Client** node at your Teradata MCP endpoint
4. Activate, open the Chat Trigger URL, upload your YAML, and start testing

---

## Tech Stack

| Tool | Role |
|------|------|
| n8n | Workflow orchestration |
| AWS Bedrock | Claude model hosting |
| Claude Sonnet | Intake, SQL generation, recommendation agents |
| Teradata MCP | SQL execution and column metadata tool |

---

## License

MIT
