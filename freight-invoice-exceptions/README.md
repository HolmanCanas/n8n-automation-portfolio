# Freight Invoice Automation — Exception Handling (n8n)

**Sector:** U.S. transportation / freight logistics
**Stack:** n8n · Google Sheets API · Google Drive API · Google Docs API · PDF merge/generation
**Status:** Production companion tool, in active use — sanitized for this portfolio (client/broker name and all spreadsheet/folder/document IDs replaced with placeholders; logic unchanged).
**Related piece:** companion to [`freight-invoice-automation`](../freight-invoice-automation) — same client account, same invoice template system.

## The problem

The main invoicing workflow batches loads by week and cross-matches them against POD PDFs automatically — it's built for the normal weekly volume. But not every load fits that pattern: a broker needs a single invoice reissued, a load was excluded from the weekly batch for a data reason, or someone needs to generate one invoice on demand without waiting for the next scheduled run. Handling those one-off cases by hand in a document editor defeats the point of automating the rest.

## What the workflow does

1. **Pulls a single broker's un-invoiced loads** from the same tracking spreadsheet used by the main automation, filtered by broker name and invoice status.
2. **Loops over each load** and matches it against the correct POD PDF by filename content rather than the strict weekly-batch matching used in the main flow — a looser, more forgiving match for one-off runs.
3. **Builds the invoice filename** from the carrier reference code and load number, consistent with the main workflow's naming convention.
4. **Copies the invoice template**, fills it with the load's data (pickup, drop, broker, date, rate, lumper fee if any, total), and generates the final document — same template system as the main workflow, so every invoice looks identical regardless of which path generated it.
5. **Uploads the finished invoice** to Drive and **writes back to the tracking spreadsheet**, marking the load as invoiced.

## Why it's worth showing

This is the piece that shows the difference between automating a process and automating *only the happy path*. Real operations always have exceptions — building a lighter, targeted tool for them (rather than forcing every edge case through the main pipeline, or leaving them to be done by hand) is a deliberate engineering decision, not a shortcut. It reuses the same spreadsheet, the same template system, and the same output convention as the main automation, so the two never produce inconsistent invoices.

## Files

- `workflow.json` — the sanitized n8n workflow export (17 nodes). Import it into n8n (`Import from File`). Spreadsheet, folder, and document IDs are placeholders — you'll need your own Google account and documents to run it live.

## Note on sanitization

The broker name and every Google Sheets/Drive/Docs ID were replaced with generic placeholders. The shared spreadsheet and template-folder placeholders are intentionally the same as in the main `freight-invoice-automation` piece — they point to the same real account, which is why both workflows are shown together.
