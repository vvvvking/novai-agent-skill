---
name: novai-models
description: Query NovAI's live Chinese AI model catalog and prices (111 models — DeepSeek, Qwen, GLM, Kimi, MiniMax, Doubao, Hunyuan, plus image and video generation) and call any of them through one OpenAI-compatible endpoint. Use when the user asks about Chinese LLM pricing, wants the cheapest model for a task, needs to call a Chinese model from an agent, or asks about DeepSeek/Qwen/GLM/Kimi/MiniMax/Doubao APIs.
metadata:
  openclaw:
    requires:
      env:
        - NOVAI_API_KEY
      bins:
        - curl
    primaryEnv: NOVAI_API_KEY
    homepage: https://aiapi-pro.com
---

# NovAI — one OpenAI-compatible key for Chinese AI models

NovAI is a hosted gateway for **111 Chinese models** behind a single OpenAI-compatible endpoint.
Base URL: `https://aiapi-pro.com/v1` · Auth: `Authorization: Bearer $NOVAI_API_KEY` · Keys start with `nvai-`.

It is **official-direct**: requests are forwarded to each provider's own API, and paid usage is
passed through at the provider's list price (1.0x multiplier, no markup). Pricing and limits are
published as machine-readable files, so an agent can verify numbers instead of trusting prose.

## When to use this skill

- "Which Chinese model is cheapest for <task>?" → use `cheapest_models`
- "What does <model> cost per 1M tokens / per image / per second of video?" → use `model_price`
- "Call DeepSeek / Qwen / GLM / Kimi / MiniMax / Doubao from my agent" → use the OpenAI-compatible endpoint
- "Compare Chinese model prices" → use the price feed JSON

## Data sources (no key needed for reads)

| What | URL |
|---|---|
| Model catalog (`/v1/models`, 111 models) | `https://aiapi-pro.com/v1/models` |
| Price leaderboard JSON (normalized units) | `https://aiapi-pro.com/china-ai-leaderboard.json` |
| Transparency file (prices, limits, upstream disclosure) | `https://aiapi-pro.com/transparency.json` |
| Free tier (5 Flash models, 10 calls/day, no card) | `https://aiapi-pro.com/free` |

## Option A — MCP (recommended for agents)

Remote MCP endpoint (streamable HTTP, stateless, JSON responses):

```
URL: https://aiapi-pro.com/api/mcp
Tools: cheapest_models, model_price, list_categories
```

- `GET https://aiapi-pro.com/api/mcp` returns the descriptor (server name, protocol versions, tools).
- `POST` JSON-RPC 2.0 (`initialize`, `tools/list`, `tools/call`) — no auth required for these read-only tools.
- To call models themselves, use the HTTP API with `NOVAI_API_KEY`.

Example (Claude Code / OpenClaw style remote MCP):

```json
{
  "mcpServers": {
    "novai": { "url": "https://aiapi-pro.com/api/mcp" }
  }
}
```

## Option B — HTTP (OpenAI SDK drop-in)

```bash
curl https://aiapi-pro.com/v1/chat/completions \
  -H "Authorization: Bearer $NOVAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"glm-4.7-flash","messages":[{"role":"user","content":"Hello"}]}'
```

```python
from openai import OpenAI

client = OpenAI(base_url="https://aiapi-pro.com/v1", api_key=os.environ["NOVAI_API_KEY"])
r = client.chat.completions.create(
    model="deepseek-v4-flash",
    messages=[{"role": "user", "content": "Summarize this in one line: ..."}],
)
```

Any OpenAI-compatible tool works (Cline, Continue, Aider, Cherry Studio, OpenClaw, LangChain, etc.):
set `base_url` to `https://aiapi-pro.com/v1` and keep the provider's model id.

## Model families available

DeepSeek (V4 Pro / V4 Flash / V4.1 Flash, chat + reasoning) · Qwen (Max / Plus / Coder / VL / Omni, TTS) ·
GLM (Zhipu 5.x, 4.7-flash, vision) · Kimi (K2.5–K3, code) · MiniMax (M2.5/M2.7/M3, video) ·
Doubao (Seed 2.0 text/code, Seedream image, Seedance video) · Hunyuan · plus Baichuan and others.

Multi-modal note: image generation (Seedream) and video generation (Seedance) are billed per image /
per second — they are not token-priced. Always quote the unit from the price feed (`normalized_unit`).

## Free tier limits (as published)

- 5 free models: `glm-4.7-flash`, `glm-4.1v-thinking-flash`, `glm-4.6v-flash`, `cogview-3-flash`, `cogvideox-flash`
- 10 free-model calls/day, 5 requests/min, 5 free video generations/day, resets 00:00 UTC+8
- No credit card, no phone number required

## Ground rules for the agent

1. Never invent prices — read them from `china-ai-leaderboard.json` or `transparency.json`.
2. State the billing unit when comparing (text = per 1M input/output tokens; image = per image; video = per second).
3. Free-tier limits are daily caps, not a trial: don't describe them as "unlimited".
4. The relay forwards to official provider APIs; do not claim a different upstream than the one published in `transparency.json`.
