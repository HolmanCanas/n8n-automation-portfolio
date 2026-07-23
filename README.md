[README.md](https://github.com/user-attachments/files/30287754/README.md)
# n8n Automation Portfolio — Holman Cañas Molina

Automation & AI workflow specialist. 10+ years in operations, regulatory, and process leadership; hands-on n8n automation delivery since 2025, currently building and maintaining production workflows at Sidekick.

Every workflow below is a real, in-production automation built for a real client — sanitized for public sharing (client names, credentials, and account-specific IDs replaced with generic placeholders; all business logic, node structure, and node counts left exactly as deployed). Where a workflow originally used a hardcoded API key or token, this version references it as an n8n environment variable (`$env.VARIABLE_NAME`) instead, matching how it would actually be configured in a live n8n instance. See each project's `README.md` for the specific sanitization notes.

Contact: holmancanas@gmail.com · [linkedin.com/in/holmancanas](https://linkedin.com/in/holmancanas)

## Projects

| Project | Sector | What it does |
|---|---|---|
| [`freight-invoice-automation`](./freight-invoice-automation) | Transportation / freight logistics | Validation-heavy weekly invoicing pipeline: matches loads to POD PDFs, batches and templates invoices, merges final client-ready packages. Cut billing time per company from 1–2 days to ~20 minutes. |
| [`freight-invoice-exceptions`](./freight-invoice-exceptions) | Transportation / freight logistics | Companion tool for one-off/exception invoices outside the normal weekly batch — same template system and output convention as the main pipeline. |
| [`fub-lead-reassignment`](./fub-lead-reassignment) | Real estate — multi-office brokerage | Auditable lead-reassignment engine: merges multi-source CRM history, applies a staged warning-before-reassignment policy, and enforces an explicit mutation allow/block list. Covers 300,000+ CRM records, 7,500+ per run, across 13 agents. |
| [`fub-zhl-extractor`](./fub-zhl-extractor) | Real estate — multi-office brokerage | Parallel, paginated CRM extraction across three offices with idempotent (match-and-update) writes to a live tracking sheet. |
| [`last-contact-resolver`](./last-contact-resolver) | Real estate — multi-office brokerage | Reconciles call history and text history per lead to resolve a single trustworthy "last effective contact" date, with confidence tagging — the data-quality layer under the reporting workflow below. |
| [`agent-roster-report`](./agent-roster-report) | Real estate — multi-office brokerage | Per-agent personalized lead-contact reports (HTML email + Slack DM) plus a consolidated management summary; includes a multi-office parameterized copy (Orange County) showing the scale-out pattern. |
| [`alm-call-scoring`](./alm-call-scoring) | Real estate — multi-office brokerage | Two-stage AI pipeline: audio chunking + Whisper transcription, then a hallucination-constrained LLM scoring pass against a call-quality framework, plus an email-intake pipeline that routes call recordings by office. |

## Stack across the portfolio

n8n (self-hosted) · REST APIs & webhooks · Follow Up Boss CRM API · Google Sheets/Drive/Docs API · Slack API · Gmail API · OpenAI (Whisper + LLM scoring) · JavaScript (Code nodes) · Docker

## How to explore a workflow

Each project folder contains a `workflow.json` (import into n8n via **Import from File**) and a `README.md` explaining the business problem, the logic, why it's non-trivial, and exactly what was sanitized. None of the credentials in these files are usable as published — you'll need your own accounts to run any of them live.
