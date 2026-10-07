# AI Social Media Auto-Poster

An n8n workflow that turns a topic and target audience into researched, platform-specific posts with AI-generated images, then publishes them to **LinkedIn, Facebook and Instagram**.

## What it does
1. A web form collects a **topic** and **target audience**.
2. One AI agent (OpenAI) researches the topic through Tavily web search, then writes a LinkedIn post, a Facebook post and an Instagram caption. It also writes two image prompts, one landscape and one square.
3. Two images are generated in parallel: a 1536x1024 image for LinkedIn and Facebook, and a 1024x1024 JPEG for Instagram.
4. Posts are published:
   - **LinkedIn** and **Facebook** receive the landscape image directly.
   - **Instagram** needs a public image URL, so the square image is uploaded to imgbb first. The workflow then creates a media container and publishes it.

```
Form -> AI Agent (OpenAI + Tavily + structured output)
          |-> Landscape image -> LinkedIn + Facebook
          '-> Square JPEG -> imgbb -> Instagram container -> Instagram publish
```

## Credentials required
| Service | n8n credential type | Used by |
|---|---|---|
| OpenAI | OpenAI API | Chat model, both image nodes |
| Tavily | Tavily API | Web research tool |
| LinkedIn | LinkedIn OAuth2 API | Post to LinkedIn |
| Facebook Page | Facebook Graph API | Post to Facebook |
| Instagram | Facebook Graph API | Create / Publish Instagram (Business account linked to your Page) |
| imgbb | Query Auth (name: `key`, value: your imgbb API key) | Upload Image to imgbb |

## Setup
1. Import `ai-social-media-auto-poster.json` into n8n (**Workflows > Import from file**).
2. Open each node that shows a credential warning and select your own credential. Placeholders look like `YOUR_..._CREDENTIAL_NAME`.
3. Replace the placeholder IDs:
   - `YOUR_LINKEDIN_PERSON_ID` in *Post to LinkedIn*
   - `YOUR_FACEBOOK_PAGE_ID` in *Post to Facebook*
   - `YOUR_INSTAGRAM_BUSINESS_ACCOUNT_ID` in *Create Instagram Container* and *Publish to Instagram*
4. Run once with **Execute workflow** and submit the test form.
5. Before activating, add **Basic Auth** to the form trigger (the production URL is public) and set an **Error Workflow** in workflow settings.

## Design notes
- A single agent call returns posts and image prompts, so there is no second LLM hop.
- The Instagram image is requested as JPEG because the Instagram Graph API doesn't accept PNG. The imgbb copy auto-deletes after 1 hour.
- Retries are enabled on generation, upload and Instagram calls. LinkedIn and Facebook posts are intentionally not retried, to avoid duplicate posts.
- The agent is capped at 5 tool iterations and the workflow has a 10-minute timeout.

## Requirements
- n8n with the LangChain/AI nodes and the community Tavily node (`@tavily/n8n-nodes-tavily`).
- Meta Graph API v23.0 access for Facebook and Instagram.
