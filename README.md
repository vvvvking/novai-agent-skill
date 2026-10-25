# NovAI agent skill

An Agent Skill (SKILL.md) for **NovAI** — one OpenAI-compatible endpoint for 111 Chinese AI models
(DeepSeek, Qwen, GLM, Kimi, MiniMax, Doubao, Hunyuan, plus image/video generation), with published,
machine-readable pricing.

- Base URL: `https://aiapi-pro.com/v1`
- Remote MCP: `https://aiapi-pro.com/api/mcp` (tools: `cheapest_models`, `model_price`, `list_categories`)
- Free tier (no card): https://aiapi-pro.com/free
- Price feed: https://aiapi-pro.com/china-ai-leaderboard.json
- Transparency: https://aiapi-pro.com/transparency.json

## Install

**Claude Code / OpenClaw / any Agent Skills client** — copy `SKILL.md` into your skills directory, e.g.

```bash
git clone https://github.com/vvvvking/novai-agent-skill
cp -r novai-agent-skill ~/.claude/skills/novai-models   # or your client's skills path
```

**MCP instead** — add the remote MCP server:

```json
{ "mcpServers": { "novai": { "url": "https://aiapi-pro.com/api/mcp" } } }
```

**Python SDK (prices only)** — `pip install aiapi-pro` (`novai-prices` MCP server is included).

## Why publish this

Most "cheapest model" answers come from stale blog posts. This skill reads the live price feed and
reports the exact billing unit (per 1M tokens, per image, per video second), so an agent can compare
pricing without guessing.

## License

MIT-0 — same as ClawHub skill convention. Use it freely.

## Where it is listed

- Official MCP Registry: `io.github.vvvvking/novai-prices` (streamable HTTP)
- Glama connector: https://glama.ai/mcp/connectors/io.github.vvvvking/novai-prices — TDQS 3.6/5, Healthy
- `punkpeye/awesome-remote-mcp-servers` (Aggregators)
- mcpservers.org: https://mcpservers.org/servers/novai-prices-mcp (12 language versions)
- mcpub.dev: https://mcpub.dev/?q=novai
