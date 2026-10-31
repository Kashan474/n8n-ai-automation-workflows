# n8n AI Automation Workflows

A collection of **AI automation projects built with [n8n](https://n8n.io)**: WhatsApp and voice assistants, email automation, RAG chatbots, multi-tool MCP agents, content generation, and guardrailed social media publishing. Each folder is a self-contained project with an importable workflow, documentation and setup steps.

![n8n](https://img.shields.io/badge/built%20with-n8n-EA4B71?logo=n8n&logoColor=white)
![Google Gemini](https://img.shields.io/badge/LLM-Google%20Gemini-4285F4?logo=googlegemini&logoColor=white)
![OpenAI](https://img.shields.io/badge/LLM-OpenAI-412991?logo=openai&logoColor=white)
![AI Agents](https://img.shields.io/badge/focus-AI%20Agents-8A2BE2)
![Status](https://img.shields.io/badge/status-actively%20maintained-brightgreen)

---

## Latest project

### PlacidWay Daily Facebook Post Automation

A scheduled, **guardrailed content pipeline** that publishes one fact-checked Facebook post per day for PlacidWay, a medical tourism marketplace. It was designed so that an AI never publishes unverified health-related claims.

```text
Schedule (daily 09:00)
  → Fetch PlacidWay news + article listings (1 req/s, robots.txt respected)
  → Pick one never-posted item (alternates news / article, de-duplicated via Google Sheets log)
  → Extract title, summary, image and body text
  → Gemini writes the post (hook, value lines, CTA, hashtags)
  → Rule-based guardrails (banned medical/outcome wording, length, numbers must exist in source)
  → Second Gemini pass: strict fact-check (fails closed if output is unreadable)
  → Optional human approval by email (Gmail send-and-wait)
  → Dry-run switch → Facebook Graph API photo or link post
  → Log every outcome to Google Sheets + alert emails + global error trigger
```

**Highlights**

- **Two-layer safety:** deterministic rules plus an LLM verifier, and the workflow fails closed when in doubt.
- **Config node** with `DRY_RUN` and `APPROVAL_MODE` switches, so it can be tested without posting anything.
- **Polite scraping:** rate-limited requests, identifying User-Agent and retries.
- **Full audit trail:** every run is logged as `PUBLISHED`, `SIMULATED`, `REJECTED_BY_REVIEWER`, `REJECTED_GUARDRAIL` or `FAILED_PUBLISH`.
- **Failure alerts:** email notifications for guardrail rejections, publish failures and unexpected workflow errors.

**Tools:** n8n, Google Gemini, Facebook Graph API, Google Sheets, Gmail

Folder: [`placidway-facebook-post-automation`](daily-social-post-agent)

---

## Projects

| # | Project | What it does | Key tools | Folder |
|---|---|---|---|---|
| 1 | **PlacidWay Daily Facebook Post Automation** | Daily scheduled pipeline that finds fresh content, writes a Facebook post with Gemini, checks it with rule-based and LLM fact-check guardrails, optionally asks for email approval, then publishes and logs the result | n8n, Gemini, Facebook Graph API, Google Sheets, Gmail | [`placidway-facebook-post-automation`](daily-social-post-agent) |
| 2 | **FCA Inquiry Email Automation** | Turns a website inquiry form into instant, personalised email replies. Gemini classifies each message (Admission / Query / Fallback), AI agents answer from an FAQ Google Sheet, and unclear messages are escalated to an admin | n8n AI Agents, Text Classifier, Gemini, Google Sheets, Gmail | [`n8n-fca-inquiry-email-automation`](n8n-fca-inquiry-email-automation) |
| 3 | **Aria – WhatsApp Multi-Agent Assistant** | AI assistant for Flying Color Academy on WhatsApp that answers parent and student questions from an FAQ knowledge base, with session memory and fallback handling | WhatsApp Cloud API, n8n AI Agent, Google Sheets | [`aria-whatsapp-multi-agent`](aria-whatsapp-multi-agent) |
| 4 | **Voice Agents (JARVIS and FCA Smart Receptionist)** | ElevenLabs voice agents that call n8n via webhook. JARVIS delegates to Gmail, Google Calendar, Airtable contacts and Tavily web-search sub-agents; the Smart Receptionist answers academy questions from a Google Sheets knowledge base | ElevenLabs Conversational AI, n8n, Gmail, Google Calendar, Airtable, Tavily | [`fca-voice-agents`](fca-voice-agents) |
| 5 | **MCP Agents** | n8n MCP Server and Client workflows that expose many tools (calculator, Wikipedia, Gmail, Google Contacts, web search, vector-store FAQ retrieval) to a single AI agent, with Redis memory, run logging and Slack notifications | n8n MCP Server/Client, Gemini, Pinecone, SerpApi, Gmail, Redis, Slack | [`MCP-agents`](MCP-agents) |
| 6 | **Pinecone RAG with Gemini Embeddings** | Ingests PDFs into a Pinecone vector index through a form trigger, then answers questions through a retriever and Q&A chain | n8n, Pinecone, Gemini embeddings, Gemini chat | [`pinecone-rag-gemini-embedding`](pinecone-rag-gemini-embedding) |
| 7 | **NovaCore HR Policy Assistant (Slack RAG Bot)** | Practice scenario: an internal HR assistant in Slack that answers leave, conduct and policy questions from a Pinecone knowledge base, with per-thread memory | n8n AI Agent, Slack, Pinecone, OpenAI | [`novacore-hr-slack-rag-bot`](novacore-hr-slack-rag-bot) |
| 8 | **AI Lead Capture & Smart Reply Agent** | Web page (built on Lovable) that sends visitor questions to an n8n webhook, where an AI agent replies and captures leads | Lovable, n8n Webhook, AI Agent, SerpApi | [`ai-lead-capture-smart-reply-agent`](ai-lead-capture-smart-reply-agent) |
| 9 | **Invoice Parser** | Watches a Google Drive folder for invoice PDFs, extracts structured data with Gemini, appends it to a Google Sheet and emails a notification written by an LLM | n8n, Google Drive, Gemini Information Extractor, Google Sheets, OpenAI, Gmail | [`invoice-parser`](invoice-parser) |
| 10 | **X Post Agent with Approval** | Form input becomes a researched X (Twitter) post drafted by an AI agent, sent for Gmail approval, then posted or sent back for revision | n8n AI Agent, Tavily, Gmail, X | [`x-post-agent-with-approval`](x-post-agent-with-approval) |
| 11 | **AI Social Media Auto-Poster** | Generates posts and matching images with AI and publishes them to LinkedIn, Facebook and Instagram | n8n, OpenAI, Tavily, LinkedIn, Facebook Graph API, Instagram | [`AI LinkedIn Auto-Poster`](AI%20LinkedIn%20Auto-Poster) |
| 12 | **Content Automation with Kie.ai (Veo3)** | End-to-end video content pipeline: script, scenes, image prompts, image generation and video generation with polling loops and Google Sheets logging | n8n, OpenAI, Kie.ai API, Google Sheets | [`content-automation-kie-ai`](content-automation-kie-ai) |
| 13 | **Nano Banana Pro Image Generator** | Automated prompt processing and API-driven image generation for content creation, storytelling, social media and thumbnails | n8n, Banana Pro API | [`nano_banana_pro_image_generator`](nano_banana_pro_image_generator) |

## Repository structure

```text
n8n-ai-automation-workflows/
├── AI LinkedIn Auto-Poster/
├── MCP-agents/
├── aria-whatsapp-multi-agent/
├── ai-lead-capture-smart-reply-agent/
├── content-automation-kie-ai/
├── fca-voice-agents/
├── invoice-parser/
├── n8n-fca-inquiry-email-automation/
├── nano_banana_pro_image_generator/
├── novacore-hr-slack-rag-bot/
├── pinecone-rag-gemini-embedding/
├── placidway-facebook-post-automation/
├── x-post-agent-with-approval/
└── README.md
```

Each project folder contains its own README with a workflow diagram, node-by-node explanation and setup guide, plus the exported workflow `.json`.

## Quick start: run any workflow

1. **Get n8n** – use [n8n Cloud](https://n8n.io/cloud/) or self-host (Docker, Railway, Node.js with a tunnel, etc.).
2. **Import** – open the project folder, download the workflow `.json`, then in n8n choose *Workflows → Import from File*.
3. **Connect credentials** – credentials are *not* included in the exports. Create your own in n8n (see the table below) and attach them to the nodes marked with a warning icon.
4. **Fill in placeholders** – replace values such as `YOUR_GOOGLE_SHEET_ID`, `YOUR_TEST_PAGE_ID`, `admin@example.com` or phone-number IDs with your own.
5. **Test, then activate** – run the workflow manually with sample data (or with `DRY_RUN` enabled where available) before switching it to *Active*.

### Credentials used across projects

| Service | n8n credential type | Used in |
|---|---|---|
| Google Gemini | Google Gemini (PaLM) API | PlacidWay, Inquiry Email, MCP Agents, Aria, Pinecone RAG, Invoice Parser |
| OpenAI | OpenAI API | NovaCore, Social Media Auto-Poster, X Post Agent, Content Automation, Lead Capture, Invoice Parser |
| Google Sheets | Google Sheets OAuth2 | PlacidWay, Inquiry Email, Aria, MCP Agents, Invoice Parser, Content Automation |
| Gmail | Gmail OAuth2 | PlacidWay, Inquiry Email, MCP Agents, Invoice Parser, X Post Agent |
| Facebook / Instagram | Facebook Graph API | PlacidWay, Social Media Auto-Poster |
| WhatsApp Business | WhatsApp Cloud API / OAuth | Aria |
| Pinecone | Pinecone API | MCP Agents, Pinecone RAG, NovaCore |
| Slack | Slack OAuth2 | NovaCore, MCP Agents |
| ElevenLabs | ElevenLabs agent + n8n webhook | Voice Agents |
| Tavily | Tavily API | Social Media Auto-Poster, X Post Agent, Voice Agents |
| SerpApi | SerpApi | MCP Agents, Lead Capture |
| Kie.ai | HTTP header auth | Content Automation |
| LinkedIn / X | LinkedIn OAuth2 / X OAuth2 | Social Media Auto-Poster, X Post Agent |

> Check each project's own README for the exact list. Some projects use additional services.

## Skills and concepts demonstrated

| Area | Details |
|---|---|
| **AI agents** | Tool-using agents, structured output parsers, intent classification, fallback routing, sub-agent delegation |
| **RAG** | FAQ grounding with Google Sheets and Pinecone vector search, Gemini and OpenAI embeddings |
| **MCP** | Building n8n MCP servers and clients that expose tools to agents |
| **Voice AI** | ElevenLabs conversational agents triggering n8n through webhooks |
| **Messaging** | WhatsApp Cloud API, Slack bots and Gmail automation |
| **Content and media** | AI writing, image and video generation (Kie.ai, OpenAI, Banana Pro), multi-platform social publishing |
| **Document processing** | PDF extraction and structured data capture from Drive into Sheets |
| **Web scraping** | Rate-limited, robots.txt-respecting page parsing in Code nodes |
| **Reliability and safety** | Human-in-the-loop approval, dry-run switches, rule-based plus LLM guardrails, fail-closed checks, audit logging, error-trigger alerts, prompt guardrails against invented answers |

## Security notes

- Exported workflows have credentials, webhook IDs, sheet IDs and personal email addresses removed or replaced with placeholders.
- Never commit API keys, tokens or `.env` files. If you fork this repo, keep your own secrets in n8n credentials.
- Review AI-generated output (emails, posts, replies) before pointing a workflow at real customers, especially in regulated topics such as health.

## Contributing and feedback

Found a bug or have an idea? Open an [issue](../../issues) or submit a pull request. Suggestions for new automations are welcome.

## Connect

- GitHub: [@Kashan474](https://github.com/Kashan474)
- LinkedIn: [Muhammad Nadeem](https://www.linkedin.com/in/nadeem-automation-specialist/)
<!-- Add your Upwork link below, then delete this comment
- Upwork: [your profile](https://www.upwork.com/freelancers/your-profile)
-->

## License

Add a license (for example [MIT](https://choosealicense.com/licenses/mit/)) via *Add file → Create new file → `LICENSE`* on GitHub.
