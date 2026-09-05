# FCA MCP Multi-Tool Agent (n8n + Gemini)

A multi-tool AI agent built in **n8n**, using the **Model Context Protocol (MCP)** to cleanly separate "which tools exist" from "who's calling them." Powered by **Google Gemini**, with a human-approval gate in front of every destructive action.

## Architecture

```
Chat message
     │
     ▼
┌─────────────────────────┐        MCP (Bearer Auth)        ┌──────────────────────────────┐
│   MCP Client with        │ ───────────────────────────────▶ │   MCP Server with Gemini     │
│   Gemini                 │ ◀─────────────────────────────── │   Embedding                  │
│   (Gemini LLM + Redis     │                                  │   (tool provider)             │
│   chat memory)            │                                  └──────────────┬────────────────┘
└───────────┬───────────────┘                                                │
            │                                                                │ ai_tool
            ▼                                                                ▼
   Google Sheets log                                          Calculator · Wikipedia · SerpAPI
   Slack success/failure                                      Google Contacts · Gmail · Calendar
   notification                                                Pinecone (FCA FAQ knowledge base)
                                                                        │
                                                                        │ delete_* actions only
                                                                        ▼
                                                        ┌────────────────────────────────────┐
                                                        │  MCP Destructive Action Approval    │
                                                        │  (sub-workflow, Slack "Approve/     │
                                                        │  Reject" gate before any delete)     │
                                                        └────────────────────────────────────┘
```

## What's in this repo

| File | Purpose |
|---|---|
| `MCP_Server_with_Gemini_Embedding.json` | Exposes all tools (Calculator, Wikipedia, SerpAPI, Google Contacts, Gmail, Google Calendar, and a Pinecone-backed knowledge base) to any MCP client over a bearer-authenticated endpoint. |
| `MCP_Client_with_Gemini.json` | The agent itself — a Gemini-powered LangChain Agent with Redis chat memory that connects to the MCP server, executes tasks, and logs every run to Google Sheets with Slack notifications. |
| `MCP_Destructive_Action_Approval.json` | A sub-workflow that gates every delete action (contact, email, calendar event) behind a Slack "Approve / Reject" prompt before anything is actually removed. |

## Features

- **MCP client/server split** — tools live on one endpoint, the agent logic lives on another, connected only by a bearer-authenticated MCP connection.
- **Multi-source knowledge routing** — the agent is prompted to pick the right source: Wikipedia for general facts, a Pinecone vector store for domain-specific FAQs, SerpAPI for anything current/real-time.
- **Human-in-the-loop for destructive actions** — deleting a contact, email, or calendar event never happens silently. Every delete request pauses for explicit Slack approval before it executes.
- **Full audit trail** — every conversation turn is logged to Google Sheets, with Slack alerts on both success and failure.
- **Persistent memory** — Redis-backed chat memory keeps context across turns.

## Prerequisites

- An [n8n](https://n8n.io) instance (cloud or self-hosted) with the LangChain (`@n8n/n8n-nodes-langchain`) and SerpAPI (`n8n-nodes-serpapi`) community/core nodes available.
- Credentials for the services below, connected in your n8n instance:

| Service | Used for |
|---|---|
| Google Gemini (Google PaLM API credential type) | Chat model + Gemini embeddings |
| Google Contacts OAuth2 | Get / create / update / delete contacts |
| Gmail OAuth2 | Read / reply / send / draft / delete emails |
| Google Calendar OAuth2 | Read / create / delete events |
| Pinecone API | Knowledge-base vector store |
| SerpApi | Real-time web search |
| Slack OAuth2 | Approval prompts + success/failure notifications |
| Redis | Chat memory |
| Google Sheets OAuth2 | Execution logging |
| HTTP Bearer Auth | Securing the MCP server endpoint / MCP client connection |

## Setup

1. **Import all three workflows** into your n8n instance (Workflows → Import from File).
2. **Attach your own credentials** to each node — every credential ID in this repo has been replaced with a placeholder (`REPLACE_WITH_YOUR_CREDENTIAL_ID`); re-select or create your own in each node.
3. **Link the approval sub-workflow**: in `MCP_Server_with_Gemini_Embedding.json`, open the three "…(requires approval)" nodes and point their workflow reference at your imported copy of `MCP_Destructive_Action_Approval.json` — easiest via the node's dropdown/search rather than typing an ID by hand.
4. **Set your Slack channel**: replace `your-slack-channel-name` in `MCP_Destructive_Action_Approval.json` (and the two `Notify` nodes in the client workflow) with your actual channel.
5. **Set your Google Sheet**: replace `REPLACE_WITH_YOUR_GOOGLE_SHEET_ID` in `MCP_Client_with_Gemini.json` with the ID of a sheet you control, with columns `Entry ID`, `Question`, `Responce`, `Failure`.
6. **Set the MCP server URL**: in `MCP_Client_with_Gemini.json`, the MCP Client node reads `$vars.MCP_SERVER_URL` — set this n8n environment variable to your deployed MCP server's endpoint.
7. **Activate all three workflows.** All three need to be active — including the approval workflow, since its Slack "Approve" button relies on n8n's wait/resume webhook, which only resolves while the workflow is active.
8. **Verify the Gemini model string** (`models/gemini-3.5-flash-lite` at time of writing) is still current in Google AI Studio — model names get renamed/deprecated across releases.

## Known limitations

- Single-value fields only for contact email/phone (index `0`) — not built for contacts with multiple emails/numbers.
- The Slack `sendAndWait` approval response shape (`$json.data.approved`) can vary slightly between n8n versions — check your test execution's output once after import.
- No pagination handling beyond the built-in `limit` parameters on "get many" tools.

## License

MIT — use, fork, and modify freely. See `LICENSE`.

## Credits

Built with [n8n](https://n8n.io), [Google Gemini](https://ai.google.dev/), and [Pinecone](https://www.pinecone.io/).


assets/MCP Client with Gemini v2.jpeg
assets/MCP Destructive Action Approval..jpeg
MCP Server with Gemini Embedding v2.jpeg


