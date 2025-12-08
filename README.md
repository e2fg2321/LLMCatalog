# LLMCatalog

A machine-readable catalog of LLM models, providers, and capabilities—designed to keep coding agents informed about what actually exists.

## The Problem

Coding agents confidently write things like:

```python
model = "gpt-4"  # SOTA model!
```

...in December 2025, because their training data is frozen in time.

They recommend deprecated APIs, hallucinate model names, cite outdated pricing, and suggest capabilities that don't exist. The models powering these agents know a lot about the world—but not about themselves or their contemporaries.

## The Solution

This repository maintains a structured, version-controlled catalog of:

- **Models** — IDs, aliases, context windows, pricing, capabilities
- **Providers** — API endpoints, documentation links, authentication patterns
- **Features** — Vision, tool use, extended thinking, structured output support

When a coding agent needs to know which model to use, what it costs, or whether it supports a specific feature—it can query this catalog instead of guessing.

## Data Structure

```
models.json
├── version          # Semantic versioning
├── last_updated     # ISO date
├── providers        # Provider metadata (13 providers)
└── models           # Model specifications (27+ models)
```

Each model entry includes:

| Field | Description |
|-------|-------------|
| `id` | Unique identifier |
| `api_model_id` | Primary API model string |
| `aliases` | Provider-specific model IDs |
| `pricing` | Input/output cost per 1M tokens (USD) |
| `context_window` | Maximum input tokens |
| `max_output_tokens` | Maximum output tokens |
| `modalities` | Input/output types (text, image, audio, video) |
| `features` | Supported capabilities |
| `thinking` | Extended reasoning parameters |
| `is_opensource` | Open-source availability |

## Current Coverage

**Providers:** Anthropic, OpenAI, Google (AI Studio + Vertex), Azure, Amazon Bedrock, OpenRouter, xAI, DeepSeek, Moonshot, MiniMax, Alibaba Qwen, Z.ai

**Models:** Claude 4.5 family, GPT-5.1 family, Gemini 2.5/3, Grok 4, DeepSeek V3.2, Kimi K2, Qwen3, and more.

## Usage

```python
import json
import urllib.request

CATALOG_URL = "https://raw.githubusercontent.com/e2fg2321/LLMCatalog/main/models.json"

with urllib.request.urlopen(CATALOG_URL) as response:
    catalog = json.loads(response.read())

# Find models with vision support
vision_models = [m for m in catalog["models"] if "vision" in m.get("features", [])]

# Get current Claude Opus pricing
opus = next(m for m in catalog["models"] if m["id"] == "claude-opus-4.5")
print(f"Input: ${opus['pricing']['input']}/1M tokens")
```

## Coming Soon

- Claude Code skills
- Cursor rules

## Contributing

PRs welcome for new models, pricing updates, and capability corrections. Please include source links for any data changes.

## License

MIT
