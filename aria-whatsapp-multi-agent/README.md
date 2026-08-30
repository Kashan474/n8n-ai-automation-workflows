# Aria – WhatsApp Multi-Agent Assistant (n8n + Gemini)

A modular, multi-agent WhatsApp assistant built in n8n. A single orchestrator workflow ("Aria") receives WhatsApp messages (text, voice notes, and images) via Meta's Cloud API, and delegates work to four specialist sub-agent workflows through n8n's `toolWorkflow` node — each one callable as an LLM "tool."

## Architecture
