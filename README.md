# Invoice Parser & Email Automation

An n8n workflow that watches a Google Drive folder for new invoice PDFs, extracts structured data from them using AI, logs every invoice to a Google Sheet, and automatically emails the billing team a clean summary — all without manual data entry.

## What it does

1. **Google Drive Trigger** — polls a specific Drive folder every minute for newly added files.
2. **Download file** — pulls the new PDF from Drive.
3. **Extract from File** — extracts raw text from the PDF.
4. **Information Extractor (Gemini 2.5 Flash)** — parses the raw text into structured fields: Invoice Number, Sender Name, Sender Email, Sender Address, Currency, Total Amount, Invoice Date, Due Date.
5. **Append row in sheet** — logs the extracted invoice data as a new row in a Google Sheet ("Invoice Datasheet").
6. **Message a model (GPT-4.1)** — drafts a professional notification email (subject + body) summarizing the invoice, using a strict system prompt (plain text, word limit, no hallucinated values).
7. **Parse Email (Code node)** — safely parses the model's JSON reply into `subject` and `body`, with fallback key handling.
8. **Send a message (Gmail)** — emails the formatted summary to the billing team.
9. **No Operation** — end-of-flow marker node.

## Workflow diagram

```
Google Drive Trigger → Download file → Extract from File → Information Extractor (Gemini)
    → Append row in sheet → Message a model (GPT-4.1) → Parse Email → Send a message (Gmail) → No Operation
```

## Setup

1. Import `Invoice_Parser.json` into your n8n instance.
2. Create/connect the following credentials in n8n and attach them to the matching nodes:
   - **Google Drive OAuth2** (Google Drive Trigger, Download file)
   - **Google Gemini (PaLM) API** (Google Gemini Chat Model)
   - **Google Sheets OAuth2** (Append row in sheet)
   - **OpenAI API** (Message a model) — or your AI Gateway equivalent
   - **Gmail OAuth2** (Send a message)
3. In the **Google Drive Trigger** node, set `folderToWatch` to the Drive folder where invoices will be uploaded.
4. In the **Append row in sheet** node, set `documentId` to your Google Sheet ID and confirm the target tab/sheet name.
5. In the **Message a model** node's system prompt, update the `Invoice Database` link to point to your actual Sheet URL.
6. In the **Send a message** node, set `sendTo` to your billing team's email address.
7. Activate the workflow.

## Credentials needed

| Service | Credential type | Used by |
|---|---|---|
| Google Drive | OAuth2 | Google Drive Trigger, Download file |
| Google Gemini (PaLM) | API key | Google Gemini Chat Model |
| Google Sheets | OAuth2 | Append row in sheet |
| OpenAI (GPT-4.1) | API key | Message a model |
| Gmail | OAuth2 | Send a message |

## Notes

- All credential IDs, folder/sheet IDs, webhook ID, instance ID, and email addresses in the JSON have been replaced with placeholders. Reconnect credentials and re-select the Drive folder / Sheet in the n8n UI after import.
- The GPT-4.1 prompt enforces plain-text output, a 120-word cap, and explicitly instructs the model never to invent or guess invoice values.
