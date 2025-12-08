---
name: llm-catalog
description: Current LLM model data - IDs, pricing, context windows, features, provider aliases. Use BEFORE writing any code that references LLM models, API calls, model selection, context limits, token pricing, or provider configurations. Consult when implementing LLM clients, choosing models, setting parameters, or estimating costs.
---

# LLM Catalog

## Purpose

This skill provides accurate, up-to-date information about LLM models to prevent hallucinating outdated model names, incorrect pricing, wrong context windows, or deprecated API identifiers.

## When to Use

**ALWAYS read the catalog BEFORE:**
- Writing code that calls any LLM API (Anthropic, OpenAI, Google, etc.)
- Setting `model` parameters in API calls
- Configuring `max_tokens`, `context_window`, or similar limits
- Implementing multi-provider LLM routing
- Writing model selection logic
- Estimating or calculating API costs
- Setting up thinking/reasoning mode parameters
- Checking if a model supports vision, tool use, or other features

**Do NOT guess model IDs or parameters from training data - they change frequently.**

## Instructions

1. **Read the catalog** at `models.json` in this repository (or fetch from https://raw.githubusercontent.com/e2fg2321/LLMCatalog/main/models.json)

2. **Extract relevant data** based on the task:
   - For API calls: use `api_model_id` and provider-specific `aliases`
   - For limits: check `context_window` and `max_output_tokens`
   - For pricing: use `pricing.input_per_1m` and `pricing.output_per_1m`
   - For capabilities: check `features` array and `modalities`
   - For thinking mode: check `thinking.parameter_format` for provider-specific syntax

3. **Use exact values** from the catalog - never approximate or use memorized data

## Catalog Structure

```json
{
  "providers": {
    "provider_id": {
      "name": "Provider Name",
      "base_url": "https://api.provider.com",
      "docs": "https://docs.provider.com"
    }
  },
  "models": [
    {
      "id": "model-id",
      "api_model_id": "exact-api-string",
      "aliases": {
        "provider": "provider-specific-id"
      },
      "pricing": {
        "input_per_1m": 1.00,
        "output_per_1m": 5.00
      },
      "context_window": 200000,
      "max_output_tokens": 64000,
      "features": ["tool_use", "vision", "streaming"],
      "thinking": {
        "supported": true,
        "parameter_format": { ... }
      }
    }
  ]
}
```

## Key Fields Reference

| Field | Use For |
|-------|---------|
| `api_model_id` | Primary model string for API calls |
| `aliases.{provider}` | Provider-specific model IDs (Bedrock, Vertex, OpenRouter) |
| `context_window` | Maximum input tokens |
| `max_output_tokens` | Maximum output tokens |
| `pricing.input_per_1m` | Cost per 1M input tokens (USD) |
| `pricing.output_per_1m` | Cost per 1M output tokens (USD) |
| `features` | Array of capabilities (vision, tool_use, streaming, etc.) |
| `thinking.parameter_format` | Provider-specific syntax for reasoning mode |
| `modalities.input` | Supported input types (text, image, audio, video) |

## Examples

### Correct API Model ID
```python
# Read from catalog - don't guess!
client.messages.create(
    model="claude-sonnet-4-5-20250929",  # From catalog api_model_id
    max_tokens=64000,  # From catalog max_output_tokens
    ...
)
```

### Multi-Provider Setup
```python
MODEL_IDS = {
    "anthropic": "claude-sonnet-4-5-20250929",
    "bedrock": "anthropic.claude-sonnet-4-5-20250929-v1:0",
    "vertex": "claude-sonnet-4-5@20250929",
    "openrouter": "anthropic/claude-sonnet-4.5"
}  # All from catalog aliases
```

### Thinking Mode Configuration
```python
# Anthropic format (from catalog)
{"thinking": {"type": "enabled", "budget_tokens": 10000}}

# OpenAI format (from catalog)
{"reasoning": {"effort": "high"}}

# Google format (from catalog)
{"generationConfig": {"thinkingConfig": {"thinkingBudget": 10000}}}
```

### Cost Estimation
```python
# Use catalog pricing, not memorized values
def estimate_cost(input_tokens, output_tokens, model_pricing):
    input_cost = (input_tokens / 1_000_000) * model_pricing["input_per_1m"]
    output_cost = (output_tokens / 1_000_000) * model_pricing["output_per_1m"]
    return input_cost + output_cost
```

## Current Coverage

**Providers:** Anthropic, OpenAI, Google (AI Studio + Vertex), Azure, Amazon Bedrock, OpenRouter, xAI, DeepSeek, Moonshot, MiniMax, Alibaba Qwen, Z.ai, IBM Granite

**Models:** Claude 4.5 family, GPT-5.1 family, Gemini 2.5/3, Grok 4, DeepSeek V3.2, Kimi K2, Qwen3, and more.
