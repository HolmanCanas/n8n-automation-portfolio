# ALM Call Transcription & Quality Scoring (n8n + OpenAI)

**Sector:** U.S. real estate — multi-office brokerage
**Stack:** n8n · OpenAI Whisper (`gpt-4o-mini-transcribe`) · OpenAI Responses API (`gpt-5.4-mini`) · Gmail API · Google Sheets API · Google Apps Script webhook · JavaScript (Code nodes, 34-node workflow)
**Status:** Production workflow, in active use — sanitized for this portfolio (a live OpenAI API key, a webhook shared secret, a Google Apps Script deployment URL, and a tracking spreadsheet ID were all fully redacted; logic unchanged).

## The problem

Sales leadership wanted every agent's first-contact call graded against a specific quality framework — did the agent ask for an **A**ppointment, explore **L**ocation preferences, and qualify **M**otivation — without anyone having to sit and listen to hundreds of call recordings a week.

## What the workflow does

**Pipeline 1 — transcription and scoring:**
1. **Reads eligible call rows** (not yet processed) from a tracking spreadsheet across three office tabs.
2. **Downloads the call recording audio** and splits it into segments to work within API limits.
3. **Transcribes each segment with OpenAI Whisper**, then reassembles the segments into one clean, ordered transcript.
4. **Sends the full transcript to an OpenAI model with a structured evaluation prompt**: the model must return strict JSON scoring the call against the ALM framework — Appointment, Location, Motivation — with every YES/NO/N/A grounded only in what's actually in the transcript (the prompt explicitly forbids inventing facts).
5. **Writes the call duration and the structured review back to the spreadsheet**, so leadership sees a scorecard, not a raw transcript they'd have to interpret themselves.

**Pipeline 2 — call-recording intake:**
6. **Monitors a shared inbox for incoming call-recording notification emails**, parses the agent, lead, phone number, call date, and recording link out of the email body (including base64/quoted-printable decoding for the email formats that require it).
7. **Routes each parsed call to the correct office** (Los Angeles / Riverside / Orange County) and forwards it to a per-office endpoint secured with a shared secret, then marks the source email as read and re-labels it so it's never processed twice.

## Why it's worth showing

This is a genuine two-stage AI pipeline, not a single API call: audio chunking to work around size limits, a transcription step separated from a reasoning/scoring step (rather than asking one model call to do both), a scoring prompt explicitly constrained against hallucination, and an intake pipeline that reliably converts unstructured emails into structured, office-routed data before the AI step ever runs. It's the clearest example in this portfolio of AI used for a specific, bounded business judgment call rather than generic chat.

## Files

- `workflow.json` — the sanitized n8n workflow export (34 nodes). Import it into n8n (`Import from File`). The OpenAI API key, a webhook shared secret, a Google Apps Script deployment URL, and the tracking spreadsheet ID are placeholders — you'll need your own OpenAI account and Google Workspace setup to run it live.

## Note on sanitization

This file contained a live OpenAI API key hardcoded in two `Authorization: Bearer` headers, a shared secret used to authenticate requests to a Google Apps Script webhook, and that webhook's real deployment URL — all fully redacted, along with the tracking spreadsheet ID. The OpenAI key and the webhook shared secret are now referenced as n8n environment variables (`$env.OPENAI_API_KEY`, `$env.APPSCRIPT_SHARED_SECRET`) rather than shown as placeholder strings. No credentials of any kind are usable from this file as published.
