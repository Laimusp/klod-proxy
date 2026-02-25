# Klod Proxy

Local HTTP proxy for Anthropic Claude API (and compatible providers) with a built-in terminal UI.

Sits between your client (Claude Code, Cursor, etc.) and one or more API providers. Balances requests across providers with round-robin, tracks token usage, and auto-retries on temporary failures — all from a single-file Python script.

![Windows](https://img.shields.io/badge/platform-Windows-blue)
![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-yellow)

## Features

- **Round-robin load balancing** — distribute requests across multiple API providers
- **Token tracking** — per-provider, per-model, and per-day usage statistics stored in SQLite
- **Auto-retry** — when a provider returns `HTTP 500` with "no available accounts" (common with shared API pools), the proxy automatically retries every 5 seconds until the request goes through, with live retry counter in the TUI
- **SSE streaming** — full support for streaming responses with real-time token counting
- **Terminal UI** — manage providers, view stats, and monitor logs without leaving the terminal
- **One-command install** — `python install.py` sets up everything and adds `klod` to PATH

## Quick Start

```bash
git clone https://github.com/PandaCIB/klod-proxy.git
cd klod-proxy
python install.py
```

After installation, reopen your terminal and run:

```bash
klod
```

Or run directly:

```bash
python proxy.py
```

## Usage

1. Start the proxy — it listens on `http://localhost:8080`
2. Go to **Providers** and add your API endpoint (URL + API key)
3. Point your client to `http://localhost:8080` instead of the real API URL

### Claude Code Setup

Set environment variables before launching Claude Code:

```bash
set ANTHROPIC_BASE_URL=http://localhost:8080
set ANTHROPIC_API_KEY=klod
```

Or in PowerShell:

```powershell
$env:ANTHROPIC_BASE_URL = "http://localhost:8080"
$env:ANTHROPIC_API_KEY = "klod"
```

The API key value can be anything (e.g. `klod`) — the proxy replaces it with the real key from the active provider. The important part is `ANTHROPIC_BASE_URL` pointing to the proxy.

### TUI Controls

| Key | Action |
|-----|--------|
| `1` | Manage providers (add, delete, toggle) |
| `2` | View token usage statistics |
| `3` | Settings |
| `0` | Clear log |

## How It Works

```
Client (Claude Code, Cursor, etc.)
        │
        ▼
   Klod Proxy (:8080)
        │
        ├──► Provider A (api.anthropic.com)
        ├──► Provider B (other-api.example.com)
        └──► Provider C (...)
```

The proxy intercepts requests, injects the API key for the selected provider, forwards the request, parses token usage from the response (including SSE streams), and logs everything to a local SQLite database.

## Requirements

- Windows 10/11
- Python 3.10+
- `aiohttp`, `rich` (installed automatically by `install.py`)

## License

MIT
