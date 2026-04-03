# Setup Guide

## Step 1 — Import the Workflow

1. In n8n go to **Workflows → ⋯ → Import from file**
2. Select `workflows/prompt_sql_tester.json`

---

## Step 2 — Configure AWS Bedrock Credentials

1. Go to **Settings → Credentials → New**
2. Search for **AWS Bedrock**
3. Fill in your **Region**, **Access Key ID**, and **Secret Access Key**
4. Save it (e.g. as `AWS Bedrock - Claude Sonnet`)
5. Assign it to all three Bedrock nodes in the workflow: **AWS Bedrock — Intake**, **AWS Bedrock — SQL**, **AWS Bedrock — Recommend**

---

## Step 3 — Set Your Model

Update the `model` field in each Bedrock node to match what you have enabled in your AWS Bedrock console:

- `anthropic.claude-3-5-sonnet-20241022-v2:0`
- `anthropic.claude-3-haiku-20240307-v1:0`

---

## Step 4 — Configure the Teradata MCP Connection

Start your Teradata MCP server in HTTP mode:

```bash
export DATABASE_URI="teradata://user:password@host:1025/database"
export MCP_TRANSPORT="streamable-http"
export MCP_HOST="0.0.0.0"
export MCP_PORT="8001"
teradata-mcp-server
```

Then in n8n:
1. Click the **MCP Client** node
2. Set **Endpoint URL** to your server address (e.g. `http://your-host:8001/mcp`)
3. Configure authentication if your MCP server requires it (Header Auth recommended for production)

> The Teradata MCP server is available at: https://github.com/Teradata/teradata-mcp-server

---

## Step 5 — Enable Tool Use in Agent Nodes

Make sure this environment variable is set on your n8n instance:

```
N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
```

---

## Step 6 — Prepare Your YAML Prompts File

See the [README](../README.md#yaml-prompts-file-format) for the expected format.

The file must include at minimum:
- One prompt with the `prompt_name` you want to test
- One prompt named `your_prompt_improver_name` (used by the recommendation agent) — rename this in the YAML and update node **7b · Extract Recommend Prompt** to match

---

## Step 7 — Activate and Run

1. Toggle the workflow to **Active**
2. Open the **Chat Trigger** URL
3. In your first message, attach your YAML file
4. Type a business question and the prompt name you want to test
5. Wait for the 3-variant run to complete — you'll receive a summary and a CSV download

---

## GDPR Checklist Before Go-Live

- [ ] Confirm testers are briefed not to use real personal data in test questions
- [ ] Set **Save Execution Progress** to `Never` in Workflow Settings
- [ ] Restrict workflow access to authorised users only
- [ ] Confirm the Teradata MCP server only connects to non-production data where possible
- [ ] Review the CSV output before sharing — it contains generated SQL referencing your schema
- [ ] Document this workflow in your Records of Processing Activities (RoPA) if it accesses personal data
