---
name: n8n-control
description: >
  Skill for interacting with n8n: list workflows, trigger executions via webhook (preferred),
  and check execution status using the public n8n REST API. Requires Node 20+.
version: 0.1.0
---

# n8n Control — Claude Skill

This skill enables Claude to operate n8n via a small CLI (`scripts/n8n.js`):
- List workflows
- Trigger a workflow (webhook-first)
- Fetch executions and status

## Requirements
- Node.js v20+
- An n8n instance (Cloud or self-hosted) with an API key
- Environment variables:
  - `N8N_API_URL` → **include** `/api/v1` (e.g. `https://n8n.example.com/api/v1`)
  - `N8N_API_KEY` → your n8n API key (header `X-N8N-API-KEY`)
  - (optional) `N8N_EXECUTE_ENDPOINT=true` → try `POST /workflows/:id/execute` if your n8n supports it
  - (optional) `N8N_WEBHOOK_SECRET` → add as `X-Webhook-Secret` header when calling webhooks

> Tip: Place a `.env` file in this folder or export env vars in the shell. The script loads `.env` automatically.

## Install (one-time)
1. Open a terminal in `~/.claude/skills/n8n-control`
2. Run:
   ```bash
   node -v
   # must be >= 20
   npm init -y >/dev/null 2>&1 || true
   npm install dotenv
   ```
3. Create `.env` with your settings (see example below).
4. Claude will now use this skill automatically when requests mention n8n tasks.

## Usage Patterns (for Claude)
- **List**:  
  Run: `node scripts/n8n.js list --active=true --limit=50`
- **Run via Webhook (preferred)**:  
  Run: `node scripts/n8n.js run --webhook "https://n8n.example.com/webhook/abcd" --json '{"foo":"bar"}'`
- **Run by Workflow ID (experimental)**:  
  If your n8n supports `POST /workflows/:id/execute`, set `N8N_EXECUTE_ENDPOINT=true`, then:  
  `node scripts/n8n.js run --id 123 --json '{"foo":"bar"}'`
- **Executions**:  
  `node scripts/n8n.js executions --status=success --limit=20`
- **Get a single execution**:  
  `node scripts/n8n.js get-exec --id <executionId>`

## Best Practices for Claude
- Prefer **webhooks** for execution. They’re stable and let you return results synchronously via **Respond to Webhook** when needed.
- When sending JSON, always pass `--json` with a valid JSON string.
- Keep logs: this script prints **structured JSON**; copy/paste outputs into reports as needed.

## References (for grounding)
- n8n API auth (use `X-N8N-API-KEY`): https://docs.n8n.io/api/authentication/  
- n8n public REST API overview: https://docs.n8n.io/api/  
- Webhook node docs: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/  
- Respond to Webhook docs: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.respondtowebhook/  
- Claude Skills (what they are and where to place them): https://docs.claude.com/en/docs/claude-code/skills and https://www.anthropic.com/news/skills
