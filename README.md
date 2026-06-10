# LeadPipe — AI lead intake on autopilot (n8n)

An importable n8n workflow that turns a messy inbox into a clean CRM:
every inbound email is read by GPT, converted into structured lead data,
routed by priority, logged to Google Sheets — and your sales channel gets
pinged instantly when a lead is hot.

```
Gmail Trigger ──→ GPT extraction ──→ Parse & validate ──→ Hot lead? ──→ 🔥 Slack alert
 (new email)      (structured JSON)   (defensive code)        │              │
                                                              └─── cold/warm ┴──→ Google Sheets
```

## What it does

1. **Gmail Trigger** — fires on every new inbound email.
2. **GPT extraction** — `gpt-4o-mini` with `response_format: json_object`
   pulls out: name, company, email, intent, budget, priority (hot/warm/cold),
   one-line summary.
3. **Parse & validate (Code node)** — defensive parsing. If the model returns
   anything malformed, the run fails loudly instead of writing garbage into
   your CRM. Unknown priorities are coerced to `warm`.
4. **Priority routing (IF node)** — hot leads take the fast lane.
5. **Slack alert** — hot leads ping `#sales` immediately with full context.
6. **Google Sheets** — every lead (hot or not) is appended with a timestamp.

## Why it's built this way

- **Structured output enforced at the API level** (`json_object` mode), not
  hoped for in a prompt — then validated again in code. Two layers, zero
  garbage rows.
- **HTTP Request node instead of a vendor node for the LLM call** — works on
  every n8n version, and swapping to Claude or a local model is a one-URL
  change.
- **Fails loud, not silent** — a broken run shows up in n8n's execution log
  with a readable error, which is what you want in production.

## Setup (10 minutes)

1. In n8n: **Workflows → Import from file** → `workflow.json`
2. Connect credentials on each node:
   - Gmail Trigger → your Google account (OAuth)
   - "Extract lead with GPT" → Header Auth credential:
     name `Authorization`, value `Bearer sk-your-openai-key`
   - Slack → your workspace, pick the channel
   - Google Sheets → select your spreadsheet and sheet
3. Create a sheet with headers:
   `name | company | email | intent | budget | priority | summary | received_at | source`
4. Activate the workflow. Send yourself a test email.

## Easy extensions

Notion or Airtable instead of Sheets (swap one node) · auto-draft a reply
with GPT and save it in Gmail drafts · enrich the company via an API ·
weekly digest of all warm leads.

## License

MIT — import it, adapt it, ship it.
# upwork-portfolio2
