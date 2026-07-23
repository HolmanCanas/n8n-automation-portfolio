# Multi-Office Last-Contact Resolver (n8n + Follow Up Boss CRM)

**Sector:** U.S. real estate — multi-office brokerage
**Stack:** n8n · Follow Up Boss (FUB) REST API (paginated, 3 offices in parallel) · Google Sheets API · JavaScript (Code nodes, 46-node scheduled workflow)
**Status:** Production workflow, runs on a schedule — sanitized for this portfolio (internal system identifier, API key, and three tracking-sheet IDs replaced with placeholders; logic unchanged).
**Related piece:** feeds the tracking sheet consumed by [`agent-roster-report`](../agent-roster-report) — this workflow's Los Angeles output sheet is that workflow's lead data source.

## The problem

"Last contact" sounds simple until you ask *last contact by what channel*. A lead might show a recent call in the CRM but an even more recent text that the call log doesn't reflect, or vice versa — and outbound-only messages shouldn't count the same as a real two-way exchange. Without resolving that properly per lead, any "days since last contact" report is only as reliable as whichever single data source it happened to check first.

## What the workflow does

1. **Runs on a schedule** and pulls the full lead list for all three offices (Riverside, Los Angeles, Orange County) from Follow Up Boss in parallel, each against its own smart list, with pagination.
2. **For every lead, pulls both call history and text history** from FUB — not just one or the other.
3. **Builds an "effective call" and "effective text" candidate per lead** — filtering out noise (e.g., missed calls with no real exchange) to find the last *meaningful* contact of each type.
4. **Resolves a single final "last effective communication"** per lead by comparing the call candidate against the text candidate and picking whichever is genuinely more recent, tagging the result with its source, direction, and a confidence label — so downstream reports can show not just a date but how sure the system is about it.
5. **Writes the resolved result back to a per-office tracking sheet**, clearing and rewriting rather than appending, so the sheet always reflects the current state rather than growing indefinitely.

## Why it's worth showing

This is the data-quality layer underneath the reporting workflow, and it's the part most automations skip: instead of trusting a single "last activity" field from the CRM, it independently resolves two different communication channels and reconciles them with explicit tie-breaking logic, at pagination scale, across three offices run in parallel. Getting this step right is what makes every report built on top of it (like the agent lead-contact report) actually trustworthy instead of quietly wrong for leads where the CRM's own "last activity" field lags reality.

## Files

- `workflow.json` — the sanitized n8n workflow export (46 nodes). Import it into n8n (`Import from File`). The FUB credential, an internal system header/key, and the three per-office tracking-sheet IDs are placeholders — you'll need your own FUB account to run it live.

## Note on sanitization

This file contained the same real internal system identifier and active API key hardcoded in a custom request header (`X-System` / `X-System-Key`) found in the related FUB workflows in this portfolio — both fully redacted. The API key is now referenced as an n8n environment variable (`$env.FUB_X_SYSTEM_KEY`) rather than shown as a placeholder string. The three per-office Google Sheets IDs were also replaced with placeholders (the Los Angeles one uses the same placeholder as in `agent-roster-report`, since it's genuinely the same sheet feeding that workflow).
