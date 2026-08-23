# Banana Pro — AI Image Generation Workflow (n8n + kie.ai)

An n8n workflow that turns a rough image idea into a polished AI-generated image, with automatic retries and logging.

## How it works

1. **Form submission** — user enters an image idea and picks a resolution (1K/2K).
2. **AI Agent (OpenAI)** — rewrites the rough idea into a detailed, cinematic image-generation prompt.
3. **kie.ai (Nano Banana 2 model)** — creates an image generation task from the prompt.
4. **Polling with retry** — checks task status twice (30s apart, ~60s max) before giving up.
5. **Safe parsing** — a Code node with try/catch extracts the image URL from kie.ai's response without crashing on malformed data.
6. **Google Drive** — downloads the generated image and uploads it to a Drive folder.
7. **Google Sheets logging** — logs the permanent Drive link and original prompt idea on success, or the failure reason on failure (separate rows/sheet).

## Setup

1. Import `Banana_Pro_image_generation_SANITIZED.json` into your n8n instance.
2. Create/attach the following credentials in n8n and select them on the matching nodes:
   - **OpenAI API** — used by the AI Agent's chat model.
   - **kie.ai API Key** (HTTP Header Auth) — used by the three kie.ai-facing HTTP Request nodes.
   - **Google Drive OAuth2** — used by the Upload to Google Drive node.
   - **Google Sheets OAuth2** — used by the two logging nodes.
3. In the **Upload to Google Drive** node, set `folderId` to a folder in your own Drive.
4. In both **Google Sheets** nodes, set `documentId` to your own spreadsheet, and make sure it has these column headers: `Prompt Idea`, `Nano Banana Image Link`, `Image Failed to Generate Reason`.
5. Create a second sheet/tab for failed rows if you want failures logged separately.
6. Run one test submission, then confirm the Google Drive node's output field name (`webViewLink`) matches what your account returns.

## Notes

- kie.ai pricing (approximate, check their dashboard for current rates): nano-banana-2 at 1K ≈ $0.04/image.
- To test without spending kie.ai credits, pin the response of a node after one real run and re-run downstream nodes from the pinned data.

## Credentials

No API keys or account IDs are stored in this workflow file — n8n only exports credential *references* (name/ID), not the secrets themselves. Placeholders like `YOUR_KIE_AI_CREDENTIAL_ID` will be replaced automatically once you select your own credentials after import.
