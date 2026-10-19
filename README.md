# Telegram Voice Note to Google Sheets

An n8n workflow that receives Telegram voice notes, transcribes them in Amharic, extracts transaction details, appends the transaction to Google Sheets, and replies in Telegram with a confirmation.

## Workflow

1. **Telegram Trigger** receives a new message.
2. **Is Voice Note?** checks whether the message contains a voice note.
3. **Download Voice File** downloads the audio from Telegram.
4. **Transcribe Amharic Audio** transcribes the recording using Google Gemini.
5. **Extract Transaction** extracts the date, amount, currency, bank, and reason using an AI agent.
6. **add_transaction_row** appends the extracted values to Google Sheets.
7. **Reply With Result** sends a confirmation back to the Telegram chat.

Non-voice messages receive a request to send a voice note.

## Import into n8n

1. Open your n8n instance.
2. Select **Workflows** and choose **Import from File**.
3. Select [`Telegram Voice Note to Google Sheets.json`](./Telegram%20Voice%20Note%20to%20Google%20Sheets.json).
4. Reconnect or create the credentials listed below.
5. Confirm the target spreadsheet and sheet.
6. Publish the workflow.

## Required credentials

The imported workflow expects these n8n credentials:

- **Telegram account** — used by the trigger, file download, and reply nodes.
- **Google Gemini (PaLM) API** — used to transcribe Amharic audio.
- **Anthropic / n8n Gateway credits** — used by the transaction extraction agent.
- **Google Sheets OAuth2** — used to append transaction rows.

Credential IDs in an exported workflow may not exist in another n8n instance. Select the corresponding credential again in each node after importing.

## Google Sheets format

Create a spreadsheet with a sheet containing these columns:

| Date | Amount | Currency | Bank | Reason |
| --- | ---: | --- | --- | --- |

The default exported workflow points to the spreadsheet named **Daily Briefing Log**. Update the spreadsheet and sheet settings in the `add_transaction_row` node if needed.

## Voice note expectations

The voice note should describe a financial transaction, including as much of the following as possible:

- Amount
- Currency
- Bank
- Reason or purpose
- Date, if it is not today

The workflow defaults unspecified currency to `ETB`, uses the current date when no date is provided, and records an unknown bank as `Unknown`. If no amount can be extracted, the agent does not append a row and explains what is missing.

## Notes

- The transcription prompt requests Amharic script and no commentary.
- Relative dates are resolved using the `Africa/Nairobi` timezone.
- Telegram triggers cannot run a test execution while the workflow is published. To use n8n's manual test execution, temporarily unpublish the workflow; otherwise send a voice note to the connected Telegram bot.
