# Pi Agent Configs

Dotfiles for the [Pi coding agent](https://github.com/earendil-works/pi-coding-agent) — providers, models, and settings.

## Quick Start

```bash
# 1. Clone directly into your pi config dir
git clone <repo-url> ~/.pi/agent

# 2. Add your secrets
cp ~/.pi/agent/.env.example ~/.pi/agent/.env
# edit ~/.pi/agent/.env → set PI_GATEWAY_BASE_URL and PI_GATEWAY_API_KEY

# 3. Resolve placeholders in models.json
set -a && source ~/.pi/agent/.env && set +a
sed -i "s|\${PI_GATEWAY_BASE_URL}|$PI_GATEWAY_BASE_URL|g" ~/.pi/agent/models.json
sed -i "s|\${PI_GATEWAY_API_KEY}|$PI_GATEWAY_API_KEY|g" ~/.pi/agent/models.json
```

Pi picks up the changes immediately (no restart needed).

## Config Overview

| File | Purpose |
|------|---------|
| `settings.json` | Theme, default provider & model |
| `models.json` | Provider endpoints and model definitions |
| `.env` *(local, not committed)* | API keys and secrets |

### Models

| Model | Role | Context |
|-------|------|---------|
| `llamacpp/qwen3-27b` | Default — local, fast | 96K |
| `openrouter/z-ai/glm-5.2` | Architect — complex reasoning | 1M |
| `openrouter/moonshotai/kimi-k2.7-code` | Implementer — code tasks | 256K |

## How It Works

The repo's `models.json` uses `${PI_GATEWAY_BASE_URL}` and `${PI_GATEWAY_API_KEY}` placeholders. After cloning, you resolve them once with `sed` using values from your local `.env`. This keeps secrets out of git.

## Updating

```bash
# Pull latest configs from repo
cd ~/.pi/agent && git pull

# Re-resolve placeholders (overwrites any manual edits)
set -a && source .env && set +a
sed -i "s|\${PI_GATEWAY_BASE_URL}|$PI_GATEWAY_BASE_URL|g" models.json
sed -i "s|\${PI_GATEWAY_API_KEY}|$PI_GATEWAY_API_KEY|g" models.json
```

## Secrets

| Excluded via `.gitignore` |
|---------------------------|
| `.env` — API keys |
| `auth.json` — session tokens |
| `sessions/` — chat history |

## Adding a New Model

Edit `models.json` → add an entry under `providers.gateway.models`:

```json
{
  "id": "provider/model-name",
  "name": "Friendly Name",
  "reasoning": true,
  "input": ["text"],
  "contextWindow": 131072,
  "maxTokens": 16384
}
```
