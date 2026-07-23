# Freight Invoice Automation (n8n)

**Sector:** U.S. transportation / freight logistics
**Stack:** n8n · Google Sheets API · Google Drive API · Google Docs API · PDF merge/generation · JavaScript (Code nodes)
**Status:** Production workflow for a real freight client — sanitized for this portfolio (all company names, spreadsheet/folder/document IDs, and client-specific codes have been replaced with placeholders; the automation logic and structure are unchanged).

## The problem

A U.S. freight/trucking company was billing clients manually: someone had to open a spreadsheet of completed loads, manually match each load against its Proof of Delivery (POD) PDF, build an invoice by hand in a document editor, and update the spreadsheet to mark it as billed — once per broker, every week. It took **1–2 days per company** to close out a billing cycle, handling around **45 invoices and 150+ verification checks per week**, with real risk of missed or duplicate charges.

## What the workflow does

1. **Pulls unbilled loads** from a Google Sheet, filtered by broker and by an "invoiced" flag.
2. **Fetches all POD PDFs** from the client's Google Drive folder for the billing period.
3. **Validates each PDF filename** against a strict naming convention (carrier code, date, load number, suffix) and rejects anything that doesn't match — catching mislabeled files before they cause a billing error.
4. **Groups loads by week** and **cross-matches** each spreadsheet row against its corresponding PDF, flagging duplicates or missing documentation instead of silently skipping them.
5. **Batches matched loads into invoices** (up to 12 line items per invoice) and computes subtotals/totals with correct currency and date formatting.
6. **Generates the invoice document** by copying a Google Docs template and merge-replacing every field (invoice number, date, rates, trip numbers, POs, totals) with real data.
7. **Converts and merges** the generated invoice with its matching POD PDFs into a single client-ready PDF package.
8. **Uploads the final package** to Google Drive and **writes back to the spreadsheet**, marking every included load as invoiced — so the next run never double-bills.

## Why it's harder than it looks

This isn't just "connect A to B" — it's a validation-heavy pipeline: filename parsing with regex, duplicate detection, week-based batching, multi-line invoice templating (up to 12 dynamic line items per document), and a write-back step that keeps the spreadsheet and the generated invoices in sync. Most of the logic lives in JavaScript Code nodes that explicitly validate and reject bad data rather than assuming the input is clean — which is what makes it safe to run unattended on a schedule.

## Impact

- Billing time per company: **1–2 days → ~20 minutes**
- Manual effort reduction: **~87%**
- Weekly volume handled: **~45 invoices, 150+ verification checks**

## Files

- `workflow.json` — the sanitized n8n workflow export. Import it directly into n8n (`Import from File`) to see the full node graph and logic. All Google Sheets/Drive/Docs IDs are placeholders (`YOUR_SPREADSHEET_ID`, etc.) — you'll need your own credentials and documents to run it live.

## Note on sanitization

The original workflow was built for a real client. For this portfolio version: the client/broker name, all Google Sheets/Drive/Docs IDs, credential labels, and one client-specific carrier code prefix were replaced with generic placeholders. The business logic, validation rules, and node structure are exactly as deployed in production.
