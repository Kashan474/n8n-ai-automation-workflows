# n8n AI Automation Workflows

A collection of production-style **AI automation projects built with [n8n](https://n8n.io)**: WhatsApp assistants, email automation, multi-tool MCP agents, content generation and social media posting. Each folder is a self-contained project with an importable workflow, documentation and setup steps.

![n8n](https://img.shields.io/badge/built%20with-n8n-EA4B71?logo=n8n&logoColor=white)
![Google Gemini](https://img.shields.io/badge/LLM-Google%20Gemini-4285F4?logo=googlegemini&logoColor=white)
![AI Agents](https://img.shields.io/badge/focus-AI%20Agents-8A2BE2)
![Status](https://img.shields.io/badge/status-actively%20maintained-brightgreen)

---

## Projects

| # | Project | What it does | Key tools | Folder |
|---|---|---|---|---|
| 1 | **FCA Inquiry Email Automation** | Turns a website inquiry form into instant, personalised email replies. Gemini classifies each message (Admission / Query / Fallback), AI agents answer from an FAQ Google Sheet, and unclear messages are escalated to an admin | n8n AI Agents, Text Classifier, Gemini, Google Sheets, Gmail | [`n8n-fca-inquiry-email-automation`](n8n-fca-inquiry-email-automation) |
| 2 | **Aria – WhatsApp Multi-Agent Assistant** | AI assistant for Flying Color Academy on WhatsApp that answers parent and student questions from an FAQ knowledge base, with session memory and fallback handling | WhatsApp Cloud API, n8n AI Agent, Google Sheets | [`aria-whatsapp-multi-agent`](aria-whatsapp-multi-agent) |
| 3 | **MCP Agents** | n8n MCP Server and Client workflows that expose many tools (calculator, Wikipedia, Gmail, Google Contacts, web search, vector-store FAQ retrieval) to a single AI agent | n8n MCP Server/Client, Gemini, Pinecone, SerpApi, Gmail | [`MCP-agents`](MCP-agents) |
| 4 | **AI LinkedIn Auto-Poster** | Generates LinkedIn posts with AI and publishes them automatically | n8n, LLM, LinkedIn | [`AI LinkedIn Auto-Poster`](AI%20LinkedIn%20Auto-Poster) |
| 5 | **Nano Banana Pro Image Generator** | Automated prompt processing and API-driven image generation for content creation, storytelling, social media and thumbnails | n8n, Banana Pro API | [`nano_banana_pro_image_generator`](nano_banana_pro_image_generator) |

## Repository structure

```text
n8n-ai-automation-workflows/
├── AI LinkedIn Auto-Poster/
├── MCP-agents/
├── aria-whatsapp-multi-agent/
├── n8n-fca-inquiry-email-automation/
├── nano_banana_pro_image_generator/
└── README.md
```

Each project folder contains its own README with a workflow diagram, node-by-node explanation and setup guide, plus the exported workflow `.json`.

## Quick start: run any workflow

1. **Get n8n** – use [n8n Cloud](https://n8n.io/cloud/) or self-host (Docker, Railway, etc.).
2. **Import** – open the project folder, download the workflow `.json`, then in n8n choose *Workflows → Import from File*.
3. **Connect credentials** – credentials are *not* included in the exports. Create your own in n8n (see the table below) and attach them to the nodes marked with a warning icon.
4. **Fill in placeholders** – replace values such as `YOUR_GOOGLE_SHEET_ID`, `admin@example.com` or phone-number IDs with your own.
5. **Test, then activate** – run the workflow manually with sample data before switching it to *Active*.

### Credentials used across projects

| Service | n8n credential type | Used in |
|---|---|---|
| Google Gemini | Google Gemini (PaLM) API | Inquiry Email, MCP Agents, Aria |
| Google Sheets | Google Sheets OAuth2 | Inquiry Email, Aria, MCP Agents |
| Gmail | Gmail OAuth2 | Inquiry Email, MCP Agents |
| WhatsApp Business | WhatsApp Cloud API / OAuth | Aria |
| Pinecone | Pinecone API | MCP Agents |
| SerpApi | SerpApi | MCP Agents |
| LinkedIn | LinkedIn OAuth2 | LinkedIn Auto-Poster |

> Check each project's own README for the exact list. Some projects use additional services.

## Skills and concepts demonstrated

| Area | Details |
|---|---|
| **AI agents** | Tool-using agents, structured output parsers, intent classification, fallback routing |
| **RAG** | FAQ grounding with Google Sheets and Pinecone vector search |
| **MCP** | Building n8n MCP servers and clients that expose tools to agents |
| **Messaging** | WhatsApp Cloud API and Gmail automation |
| **Integrations** | Google Workspace, Pinecone, SerpApi, LinkedIn, image-generation APIs |
| **Reliability** | Human-in-the-loop escalation, session memory, prompt guardrails against invented answers |

## Security notes

- Exported workflows have credentials, webhook IDs and personal email addresses removed or replaced with placeholders.
- Never commit API keys, tokens or `.env` files. If you fork this repo, keep your own secrets in n8n credentials.
- Review AI-generated output (emails, posts, replies) before pointing a workflow at real customers.

## Contributing and feedback

Found a bug or have an idea? Open an [issue](../../issues) or submit a pull request. Suggestions for new automations are welcome.

## Connect

- GitHub: [@Kashan474](https://github.com/Kashan474)
<!-- Add your links below, then delete this comment
- LinkedIn: [your-name](https://www.linkedin.com/in/your-profile)
- Upwork: [your profile](https://www.upwork.com/freelancers/your-profile)
-->

## License

Add a license (for example [MIT](https://choosealicense.com/licenses/mit/)) via *Add file → Create new file → `LICENSE`* on GitHub.
