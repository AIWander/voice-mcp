# voice-mcp

[![Build](https://github.com/AIWander/voice-mcp/actions/workflows/build.yml/badge.svg)](https://github.com/AIWander/voice-mcp/actions/workflows/build.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Rust MCP server for voice — TTS via [edge-tts](https://github.com/rany2/edge-tts), STT via `voice_server.py` wrapper, with checkpoint recovery. Prebuilt Windows binaries (ARM64 + x64) on [Releases](https://github.com/AIWander/voice-mcp/releases).

Pairs with **[AIWander/Voice-Command](https://github.com/AIWander/Voice-Command)**, which provides the Python `voice_server.py` listening server (microphone capture, faster-whisper transcription, emotion detection).

---

## What it does

`voice-mcp` is a STDIO MCP server. It exposes 10 voice tools to any MCP-capable AI client (Claude Desktop, Cowork, Claude Code, Codex CLI, Gemini CLI, LM Studio, etc.):

| Tool | What it does |
|------|-------------|
| `speak` | Speak text aloud via edge-tts neural voices |
| `speak_and_listen` | Speak, wait for playback to finish, then immediately listen for a reply |
| `listen_for_speech` | Listen and transcribe what the user says |
| `start_voice_mode` | Check voice server readiness; loads recent session history |
| `voice_checkpoint` | Save the current session transcript to a file |
| `voice_load_checkpoint` | Restore a transcript from a checkpoint file |
| `voice_get_transcript` | Return the current in-memory session transcript |
| `voice_add_note` | Add a note to the transcript without going through speech |
| `list_voices` | List available edge-tts voices |
| `get_config` | Return the current voice configuration |

For a lighter 3-tool Python wrapper, see `server.py` in [AIWander/Voice-Command](https://github.com/AIWander/Voice-Command).

---

## Requirements

- **Windows 10 or 11** (x64 or ARM64)
- **[edge-tts](https://github.com/rany2/edge-tts)** on PATH: `pip install edge-tts`
- **`voice_server.py` running on `localhost:5123`** — the Python listener that does mic capture and STT. Set it up via [AIWander/Voice-Command](https://github.com/AIWander/Voice-Command).

---

## Install

### Option 1 — Download prebuilt binary

From the [latest release](https://github.com/AIWander/voice-mcp/releases/latest):

- **`voice-mcp-x64.exe`** — most Windows PCs
- **`voice-mcp-arm64.exe`** — Copilot+ / Snapdragon X devices

Drop it anywhere you like (e.g. `C:\CPC\servers\voice-mcp.exe`).

### Option 2 — Build from source

```bash
git clone https://github.com/AIWander/voice-mcp
cd voice-mcp
cargo build --release
# binary: target/release/voice-mcp.exe
```

Requires [Rust](https://rustup.rs/) stable toolchain.

---

## Wire it into your AI client

Add this to your MCP config. For Claude Desktop that's `%APPDATA%\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "voice": {
      "command": "C:\\path\\to\\voice-mcp.exe"
    }
  }
}
```

Restart your AI client after editing. In Claude Desktop and Cowork, also make sure the voice connector is toggled **on** in **Settings → Connectors**.

---

## Config

The binary reads `voice.config.toml`. Set the `VOICE_CONFIG_PATH` environment variable to point at your config file, or place it at `C:\CPC\voice\voice.config.toml` (the default path). Example:

```toml
[edge-tts]
voice = "en-US-GuyNeural"
speed = 1.0
pitch = "+0Hz"
volume = 1.0

[listen]
silence_timeout_secs = 4.0
min_speech_duration_secs = 4.0
rms_threshold = 100
pre_record_enabled = true
noise_filter_enabled = true
listen_timeout_secs = 120
```

All fields are optional — the binary uses the defaults shown above if no config file is found.

---

## How it works

```
AI client (Claude Desktop, etc.)
    ↕ STDIO (JSON-RPC 2.0)
voice-mcp.exe          ← this repo
    ↕ edge-tts CLI     (TTS: text → MP3 → PowerShell MediaPlayer)
    ↕ HTTP :5123       (STT: POST /listen → transcription)
voice_server.py        ← AIWander/Voice-Command
    ↕ microphone + faster-whisper
```

---

## License

Apache 2.0 — see [LICENSE](LICENSE).
