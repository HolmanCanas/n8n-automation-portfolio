# Agent Roster & Weekly Lead-Contact Report (n8n)

**Sector:** U.S. real estate — multi-office brokerage
**Stack:** n8n · Google Sheets API · Slack API (bot + DM) · Gmail API · HTML email generation · JavaScript (Code nodes, 29-node workflow)
**Status:** Production workflow, in active use, one of several per-office copies — sanitized for this portfolio (client company name, manager/admin email, Slack bot token, Slack user ID, and all spreadsheet/image IDs replaced with placeholders; logic unchanged).
**Related piece:** consumes the tracking sheet produced by [`last-contact-resolver`](../last-contact-resolver) — that workflow's Los Angeles output is this workflow's lead data source.

## The problem

With leads and agents split across office-specific views in the CRM, there was no single, personalized way to tell each agent exactly which of their leads had gone quiet — and no consolidated view for management to see contact performance across the whole office without pulling it together by hand every week.

## What the workflow does

1. **Pulls the agent roster and the office's full lead list** from Google Sheets.
2. **Matches each lead to its assigned agent**, computing how long it's been since last contact.
3. **Builds a personalized report per agent** — an HTML email listing their own leads that need attention, color-coded by urgency — plus a matching Slack DM, with the agent's photo pulled live from Slack for the notification.
4. **Builds a consolidated admin summary** across the whole office and emails it to management separately from the individual agent reports.
5. **Snapshots the report to a history sheet** and keeps a rolling "last report" table (clearing and rewriting it each run) so week-over-week comparisons are possible without the sheet growing unbounded.
6. **Ships with a dry-run switch** (`DRY_RUN_EMAIL` / `DRY_RUN_SLACK_ID`) that redirects every email and Slack DM to a single test recipient instead of the real agents — the same safety pattern used across this automation suite for testing changes before they reach a live team.
7. **Runs as a callable sub-workflow** (`Execute Workflow Trigger`), invoked per office from a parent workflow — this is one of several near-identical office-specific copies.

## Why it's worth showing

Personalized, per-recipient reporting at team scale is a different problem than a single aggregate report: it means matching, branching, and formatting logic that runs once per agent, rate-limited delivery (Slack DMs are throttled to avoid hitting API limits), and a dry-run mode that makes it safe to test formatting or logic changes without spamming a real sales team. It's also a good example of the multi-office pattern used across this suite — the same workflow logic, parameterized and duplicated per market.

## Files

- `workflow.json` — the sanitized n8n workflow export for the **Los Angeles** office (29 nodes). Import it into n8n (`Import from File`). Google Sheets/Drive IDs, the Slack bot token, a Slack user ID, and the manager's email are placeholders — you'll need your own accounts and credentials to run it live.
- `workflow-orange-county.json` — the **Orange County** copy of the same workflow (28 nodes), included to show the multi-office pattern in practice: same logic, parameterized per market, invoked by a parent workflow per office. Sanitized the same way as the main file.

## Note on sanitization

This file contained two real, active credentials in plain text: a Slack bot token (`xoxb-...`) hardcoded in a Code node, and the client's real company name embedded in an HTML email template (as both visible text and an image `alt` attribute) and in the client's Follow Up Boss subdomain. All of these were fully redacted, along with five Google Sheets/Drive resource IDs, a Slack user ID, and two real email addresses. The Slack bot token is now referenced as an n8n environment variable (`$env.SLACK_BOT_TOKEN`) rather than shown as a placeholder string, in both this file and the Orange County copy. No credentials of any kind are usable from this file as published.
