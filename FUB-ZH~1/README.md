# FUB Pre-Approved Lead Extractor (n8n + Follow Up Boss CRM)

**Sector:** U.S. real estate — multi-office brokerage
**Stack:** n8n · Follow Up Boss (FUB) REST API (paginated) · Google Sheets API
**Status:** Production workflow, in active use — sanitized for this portfolio (internal system identifier, API key, and tracking spreadsheet ID replaced with placeholders; logic unchanged).

## The problem

Mortgage pre-approval status (from Zillow Home Loans) lives in Follow Up Boss as a tag on the lead record, scattered across three separate office pipelines (Los Angeles, Orange County, Riverside). There was no single, always-current list of "who's pre-approved and ready to move" that leadership could check without manually filtering three different CRM views.

## What the workflow does

1. **Queries Follow Up Boss in parallel for all three offices**, each against its own saved smart list, with full pagination handling so no leads are missed on large result sets.
2. **Flattens and normalizes each office's lead list** (id, name, stage, assigned agent, tags, last activity) into a consistent shape.
3. **Filters each office's leads for the "Pre-approved" tag** and stamps each surviving lead with its office of origin.
4. **Merges all three offices into a single dataset.**
5. **Writes the result to a tracking spreadsheet**, matching on lead ID so re-runs update existing rows instead of duplicating them — the sheet is always a current snapshot, not an ever-growing log.

## Why it's worth showing

It's a small workflow on the surface, but it demonstrates three things that matter in production automation: parallel API calls against the same endpoint with per-office parameters (rather than one query plus manual filtering), correct pagination handling against a REST API that returns `nextLink`-style cursors, and idempotent writes (match-and-update instead of append-only) so the output stays trustworthy on every run rather than accumulating duplicates over time.

## Files

- `workflow.json` — the sanitized n8n workflow export (18 nodes). Import it into n8n (`Import from File`). The FUB credential, an internal system header/key, and the tracking spreadsheet ID are placeholders — you'll need your own FUB account to run it live. FUB smart list IDs (per office) were left as illustrative numeric examples; they're meaningless without the associated account.

## Note on sanitization

This file originally contained a real internal system identifier and an active API key value hardcoded in a custom request header (`X-System` / `X-System-Key`) — both were fully redacted, along with the tracking spreadsheet ID. The API key is now referenced as an n8n environment variable (`$env.FUB_X_SYSTEM_KEY`) rather than shown as a placeholder string, matching how it would actually be configured in a live n8n instance. No credentials of any kind are usable from this file as published.
