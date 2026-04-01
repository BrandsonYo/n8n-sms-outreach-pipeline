# n8n SMS Outreach Pipeline

Automated SMS cold outreach system built in n8n. Pulls leads from a Google Sheet, sends personalised SMS messages via TextBee, and tracks replies with automatic follow-up sequencing.

---

## What it does

```
Google Sheets (Leads tab)
  → filter leads by status (New / Ready)
  → send personalised SMS via TextBee API
  → log sent message + timestamp back to sheet
  → trigger follow-up sequence (F1 at 4h, F2 at 24h if no reply)
```

Built for cold outreach at scale. Each run processes a configurable batch size. Designed to be triggered on a schedule (e.g. daily at 9am).

---

## Stack

- **n8n** — automation platform (self-hosted or cloud)
- **TextBee** — SMS gateway API (NZ/AU numbers supported)
- **Google Sheets** — lead list and conversation log

---

## Setup

### 1. Prerequisites

- n8n instance running (self-hosted or n8n.cloud)
- TextBee account with an active Android device registered
- Google Sheets spreadsheet with a `Leads` tab

### 2. Google Sheets structure

The `Leads` tab needs at minimum:

| Column | Description |
|---|---|
| Name | Lead's full name |
| Phone | Mobile number (E.164 format recommended, e.g. `+6421...`) |
| Status | `New`, `F1 Sent`, `F2 Sent`, `Replied`, `Not Interested` |
| Last Contact | Timestamp of last outbound message |
| Message Sent | The message body that was sent |

### 3. Credentials (set up in n8n UI)

Create these credentials in your n8n instance before importing:

| Credential name | Type | Notes |
|---|---|---|
| Google Sheets account | Google Sheets OAuth2 | Connect your Google account |
| Textbee API Key | Header Auth | Header name: `x-api-key`, value: your TextBee API key |

After creating each credential, copy the credential ID from the URL (`/credentials/CREDENTIAL_ID/edit`) — you'll need these when configuring nodes.

### 4. Environment variables

Copy `.env.example` to `.env` and fill in your values:

```bash
cp .env.example .env
```

| Variable | Where to find it |
|---|---|
| `TEXTBEE_API_KEY` | TextBee dashboard → API Keys |
| `TEXTBEE_DEVICE_ID` | TextBee dashboard → Devices → your device ID |
| `GOOGLE_SHEETS_SPREADSHEET_ID` | Google Sheets URL: `spreadsheets/d/SPREADSHEET_ID/edit` |

### 5. Import the workflow

1. In n8n: **Workflows → Import from file**
2. Select `workflow.json`
3. Update the credential IDs in each Google Sheets node and the Send SMS node to match the credentials you created in step 3
4. Update the TextBee device ID in the Send SMS node URL
5. Set your spreadsheet ID in each Google Sheets node
6. Activate the workflow

---

## Personalisation

The message template uses fields from the Leads sheet. Modify the `Build Message` Code node to change the message copy.

---

## Follow-up sequencing

The workflow checks `Last Contact` timestamp to determine whether a lead is due for F1 (first follow-up) or F2 (second follow-up). Adjust the timing thresholds in the routing nodes.

---

## License

MIT
