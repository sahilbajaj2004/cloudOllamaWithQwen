# Sahil Bajaj: Qwen3-Coder 30B-A3B Q4_K_M on Kaggle

**Author:** Sahil Bajaj

This project runs `qwen3-coder:30b-a3b-q4_K_M` on a Kaggle GPU runtime using Ollama and exposes an Anthropic-compatible endpoint for Claude Code through a Cloudflare Quick Tunnel.

## Architecture

```text
Claude Code
    ↓
Cloudflare Quick Tunnel
    ↓
Kaggle Notebook
    ↓
Ollama
    ↓
Qwen3-Coder 30B-A3B Q4_K_M
    ↓
NVIDIA Tesla T4 ×2
```

## Model

```text
qwen3-coder:30b-a3b-q4_K_M
```

The notebook is specifically built for Qwen3-Coder 30B-A3B Q4_K_M.

## Requirements

- Kaggle Notebook
- GPU: NVIDIA Tesla T4 ×2
- Kaggle Internet: ON
- Claude Code
- Sufficient storage for the model
- Cloudflare Quick Tunnel access

## Notebook workflow

### First-time setup

Run the setup blocks near the top of the notebook. They prepare the environment, Ollama, Qwen3-Coder, API testing, benchmarking, and Cloudflared.

### SERVER ON (Sahil Bajaj)

The auto-recovery ON block checks the runtime and:

1. Installs missing system dependencies.
2. Installs Ollama if missing.
3. Installs cloudflared if missing.
4. Starts Ollama.
5. Checks for `qwen3-coder:30b-a3b-q4_K_M`.
6. Downloads the model if it is missing.
7. Starts a fresh Cloudflare Quick Tunnel.
8. Waits for DNS propagation.
9. Tests `/v1/messages`.
10. Prints the Claude Code `settings.json`.

### SERVER OFF (Sahil Bajaj)

Stops the Ollama and Cloudflare processes without intentionally shutting down the Kaggle notebook.

## Claude Code configuration

The ON block generates:

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://YOUR-TUNNEL.trycloudflare.com",
    "ANTHROPIC_AUTH_TOKEN": "ollama",
    "ANTHROPIC_MODEL": "qwen3-coder:30b-a3b-q4_K_M",
    "CLAUDE_CODE_DISABLE_UNKNOWN_MODEL_WINDOW_ENFORCEMENT": "1"
  },
  "model": "qwen3-coder:30b-a3b-q4_K_M",
  "theme": "dark"
}
```

Use the latest tunnel URL printed by the ON block.

## Claude Code model-catalog workaround

Claude Code may not recognize the Ollama model ID in its local model catalog. The notebook therefore sets:

```text
CLAUDE_CODE_DISABLE_UNKNOWN_MODEL_WINDOW_ENFORCEMENT=1
```

The Ollama configuration uses a 65,536-token context rather than claiming a 1M context.

## Kaggle session resets

When a Kaggle runtime ends, running processes and GPU state are lost:

```text
Ollama process       → starts again
Qwen model in VRAM   → loads again
Cloudflare tunnel    → starts again
Tunnel URL           → may change
```

The auto-recovery ON block is designed to restore the required runtime components.

If the model files are not retained in the configured storage, Ollama will need to download the model again.

## Branding

The notebook's major markdown and executable blocks are branded with **Sahil Bajaj** where it is appropriate, including:

- setup blocks
- GPU verification
- Ollama/model blocks
- API testing
- benchmark
- Cloudflare/tunnel blocks
- SERVER ON
- SERVER OFF
- notebook metadata

## Author

**Sahil Bajaj**
