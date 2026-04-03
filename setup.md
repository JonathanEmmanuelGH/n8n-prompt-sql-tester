# Setup Guide

## Step 1 — Import the Workflow

1. In n8n go to **Workflows → ⋯ → Import from file**
2. Select `workflows/pii_extraction.json`

---

## Step 2 — Configure AWS Bedrock Credentials

1. Go to **Settings → Credentials → New**
2. Search for **AWS Bedrock**
3. Fill in your **Region**, **Access Key ID**, and **Secret Access Key**
4. Save it (e.g. as `AWS Bedrock - Claude Sonnet`)
5. In the workflow, click each **AWS Bedrock** node and select this credential

---

## Step 3 — Set Your Model

Each AWS Bedrock node has a `model` field. Update it to match the Claude model you have enabled in your AWS Bedrock console, for example:

- `anthropic.claude-3-5-sonnet-20241022-v2:0`
- `anthropic.claude-3-haiku-20240307-v1:0`

---

## Step 4 — Activate and Test

1. Toggle the workflow to **Active**
2. Copy the **Form Trigger** URL from the node
3. Open it in a browser and upload a test document
4. Download the resulting `pii_results.csv`

---

## GDPR Checklist Before Go-Live

- [ ] Confirm you have a legal basis to process the documents being scanned
- [ ] Set **Save Execution Progress** to `Never` in Workflow Settings
- [ ] Restrict workflow access to authorised users only
- [ ] Confirm your AWS region complies with your data residency requirements
- [ ] Establish a retention policy for the output CSV files
- [ ] Document this workflow in your Records of Processing Activities (RoPA)
