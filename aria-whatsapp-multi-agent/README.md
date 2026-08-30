# Aria – WhatsApp Multi-Agent Assistant (n8n + Gemini)

A modular, multi-agent WhatsApp assistant built in n8n. A single orchestrator workflow ("Aria") receives WhatsApp messages (text, voice notes, and images) via Meta's Cloud API, and delegates work to four specialist sub-agent workflows through n8n's `toolWorkflow` node — each one callable as an LLM "tool."

## Architecture
WhatsApp (Meta Cloud API)
│
▼
Aria WhatsApp Orchestrator (WhatsApp_Ai_Agent_with_2_Webhooks_-_Fixed.json)
├─ handles GET webhook (Meta verification challenge)
├─ handles POST webhook (incoming messages)
├─ routes by message type: text / audio / image
├─ transcribes voice notes (Groq Whisper) and analyzes images (Gemini Vision)
├─ logs failures to a Google Sheet + Slack alert
└─ calls sub-agents as tools:
├─ Serp Agent (Serp_Agent_with_Gemini.json) – live web search via SerpAPI
├─ Contact Agent (Contact_Agent_with_Gemini.json) – Google Contacts CRUD
├─ Gmail Agent (Gmail_Agent_with_Gemini.json) – read/send/reply/delete email
└─ Calendar Agent (Calender_Agent.json) – Google Calendar CRUD


Each sub-agent is a **standalone n8n workflow** with its own `Execute Workflow Trigger`, its own Gemini model node, and its own set of tool nodes — the orchestrator calls each one via a `toolWorkflow` node, passing the user's query as input.

## What it does

- **Text messages** → passed straight to the Aria AI Agent.
- **Voice notes** → downloaded from WhatsApp, transcribed with Groq's `whisper-large-v3-turbo`, then passed to the AI Agent.
- **Images** → downloaded from WhatsApp, captioned/analyzed with Gemini Vision, then passed to the AI Agent.
- **Unsupported types** → a friendly fallback message.
- The AI Agent decides — per message — whether to answer directly or call one of the four sub-agent tools (web search, contacts, Gmail, calendar).
- Replies are sent back to the user over WhatsApp.
- Any node failure is caught, logged to a Google Sheet, and posted to a Slack channel for review.

## Setup

### 1. Import the workflows
Import all five JSON files into your n8n instance (`aria-whatsapp-orchestrator.json`, `serp-search-agent.json`, `contact-agent.json`, `gmail-agent.json`, `calendar-agent.json`). Import the four sub-agents **first**, then the orchestrator — the orchestrator's `toolWorkflow` nodes reference the sub-agents by workflow ID, so after import you'll need to re-select each sub-agent workflow inside the orchestrator's four "Call '…'" tool nodes (see step 5).

### 2. Credentials to create
| Credential type | Used by | Notes |
|---|---|---|
| Google Gemini (PaLM) API | All 5 workflows | Single Gemini API key, reused everywhere |
| WhatsApp Business Cloud API | Orchestrator | Phone Number ID + permanent access token from Meta |
| HTTP Header Auth | Orchestrator (Transcribe node) | Header `Authorization: Bearer <GROQ_API_KEY>` for Groq Whisper transcription |
| Google Sheets OAuth2 | Orchestrator (error logging) | Needs edit access to your error-log spreadsheet |
| Slack OAuth2 | Orchestrator (alerts) | Needs `chat:write` scope for your alerts channel |
| SerpAPI | Serp Agent | API key from serpapi.com |
| Google Contacts OAuth2 | Contact Agent | Needs Contacts read/write scope |
| Gmail OAuth2 | Gmail Agent | Needs Gmail read/send/modify scope |
| Google Calendar OAuth2 | Calendar Agent | Needs Calendar read/write scope |

### 3. Environment variable
Set `WHATSAPP_VERIFY_TOKEN` in your n8n instance — this must match the "Verify Token" you register in the Meta App dashboard when you subscribe your WhatsApp webhook.

### 4. Meta WhatsApp webhook
- Webhook URL: `https://<your-n8n-domain>/webhook/whatsapp`
- Point Meta's GET (verification) and POST (message) callbacks at the same path — this workflow uses two separate webhook nodes (`Get Webhook` for GET, `Post Webhook` for POST) both on path `whatsapp`.
- Subscribe to the `messages` field.

### 5. Re-link the sub-agent tool nodes
After importing, open the orchestrator and re-point each of these four `toolWorkflow` nodes at the newly-imported workflow (n8n auto-fills the ID once you select from the dropdown):
- `Call 'Serp Agent with Gemini'`
- `Call 'Contact Agent with Gemini'`
- `Call 'Gmail Agent with Gemini'`
- `Call 'Calender Agent'`

### 6. Fill in placeholders
Search each JSON for `YOUR_...` placeholders and replace with your actual values (calendar email, WhatsApp phone number ID, Google Sheet ID, Slack channel), then reattach every credential (all were stripped for GitHub).

### 7. Activate
Activate the four sub-agent workflows first, then the orchestrator.

## Notes
- All credential IDs, workflow IDs, sheet/channel IDs, and personal identifiers in these files are placeholders — nothing here is live/functional until you fill in your own.

- The orchestrator also references a Pinecone "Academy Knowledge Base" tool in its system prompt that isn't included in this workflow's node list in this export — add it separately if you use it.

- - <img width="1690" height="931" alt="ChatGPT Image Aug 30, 2026, 12_23_21 PM" src="https://github.com/user-attachments/assets/e86f18cd-2e5e-4f74-a38a-75d082454477" />
