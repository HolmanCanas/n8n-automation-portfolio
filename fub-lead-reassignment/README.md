# Buyer Lead Reassignment & Audit (n8n + Follow Up Boss CRM)

**Sector:** U.S. real estate — multi-office brokerage
**Stack:** n8n · Follow Up Boss (FUB) REST API · Slack API · Gmail API · JavaScript (Code nodes, 45-node workflow)
**Status:** Production workflow, in active use — sanitized for this portfolio (client email domain, internal Slack channel IDs, and one contact's name have been replaced with placeholders; all business logic and node structure are unchanged).

## The problem

Buyer leads in the CRM go cold when an agent doesn't follow up in time — no one was systematically checking last-contact dates across the full pipeline. Manually cross-referencing tasks, texts, calls, and notes per lead to decide who needed a nudge or a reassignment was the kind of audit that, done by hand across a large multi-agent team, could eat **the equivalent of three weeks of manual work per agent**.

## What the workflow does

1. **Validates candidate leads** for the audit (Phase I filtering).
2. **Pulls the full communication history per lead** from Follow Up Boss: open tasks, text messages, calls, and notes.
3. **Builds a unified lead profile** merging all of that into one record per lead — last contact date, agent, communication channel history.
4. **Runs each lead through a rules engine** (`EXE Router`) that decides one of three outcomes: no action, a warning note to the agent (lead approaching the neglect threshold), or reassignment to a shared "Buyer Reassignment Pond" (lead past the threshold with no action taken).
5. **Prepares the CRM mutation with an explicit allow/block list** — only pond reassignment and note creation are permitted; stage changes and task creation are hard-blocked at the code level, so the automation can never do more than it's meant to.
6. **Ships with a DRY_RUN / REPORT_ONLY / EXECUTE mode switch** — the workflow can simulate a full run and produce the audit report without touching a single live record, which is how every new rule gets validated before it's allowed to write to the CRM.
7. **Executes the approved mutations** against the FUB API — reassigning the pond and/or creating notes, always preserving existing tags and only adding a tracking tag for traceability.
8. **Builds and sends a full audit report** (email + Slack) listing exactly what changed and why, for management visibility.

## Why it's harder than it looks

This isn't a simple "if no contact, reassign" rule — it's an auditable decision pipeline: multi-source data merging (tasks/texts/calls/notes) per lead, a staged warning-before-reassignment policy so agents get a fair chance to act, an explicit mutation allowlist/blocklist as a safety rail against the automation overreaching, and a dry-run mode that lets new logic be validated risk-free against real data before it's allowed to execute. That safety-first design is what makes it trustworthy to run unattended against a live CRM with thousands of active leads.

## Impact

- CRM records covered: **300,000+ total, 7,500+ in each audit run**
- Team size covered: **13 agents** across 3 U.S. metro markets
- Manual audit time compressed: **~3 weeks of manual work per agent → a few hours**

## Files

- `workflow.json` — the sanitized n8n workflow export (45 nodes). Import it into n8n (`Import from File`) to see the full logic. Follow Up Boss and Slack credentials, plus a couple of account-specific IDs (Slack channel IDs, FUB pond/smart list IDs), are placeholders — you'll need your own FUB account and credentials to run it live.

## Note on sanitization

The original workflow runs against a real brokerage's Follow Up Boss account. For this portfolio version: the notification email address (which revealed the client's domain), two internal Slack channel IDs, one contact's name embedded in a variable name, an internal system identifier, and an active API key hardcoded in a custom request header (`X-System` / `X-System-Key`) were all replaced with generic placeholders. Follow Up Boss pond/smart-list IDs were left as illustrative numeric examples — they're meaningless without the associated FUB account. All business rules, safety guardrails, and node structure are exactly as deployed in production. The internal system API key is referenced as an n8n environment variable (`$env.FUB_X_SYSTEM_KEY`) rather than shown as a placeholder string, matching how it would actually be configured in a live n8n instance.
