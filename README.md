# voice-mcp

[![Build](https://github.com/AIWander/voice-mcp/actions/workflows/build.yml/badge.svg)](https://github.com/AIWander/voice-mcp/actions/workflows/build.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

`voice-mcp` is the Rust MCP wrapper for the AIWander voice stack. It speaks through `edge-tts` and sends listening/transcription work to the separate Python `voice_server.py` process running on `http://localhost:5123`.

It pairs with [AIWander/Voice-Command](https://github.com/AIWander/Voice-Command), which ships the Python listener, `voice_server.py`, and the surrounding setup/docs.

## Core tools

The main voice workflow is built around these 7 tools:

- `speak`
- `listen_for_speech`
- `speak_and_listen`
- `start_voice_mode`
- `voice_checkpoint`
- `voice_load_checkpoint`
- `voice_get_transcript`

Current builds also keep a few compatibility utilities (`voice_add_note`, `list_voices`, `get_config`) used by the broader CPC voice stack.

## Install

### Option 1: Download a prebuilt binary

Grab the right Windows binary from [Releases](https://github.com/AIWander/voice-mcp/releases/latest):

- `voice-mcp-x64.exe`
- `voice-mcp-arm64.exe`

### Option 2: Install from source

```powershell
cargo install --path .
```

That installs `voice-mcp.exe` into Cargo's bin directory.

## MCP config

Add it to your MCP config, for example `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "voice": {
      "command": "path/to/voice-mcp.exe"
    }
  }
}
```

## Required listener

`voice-mcp` does not capture microphone audio by itself. You must run the Python `voice_server.py` listener separately on `localhost:5123`. Setup instructions live in [AIWander/Voice-Command](https://github.com/AIWander/Voice-Command).

## License

Apache 2.0. See [LICENSE](LICENSE).
