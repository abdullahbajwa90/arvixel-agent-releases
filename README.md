# Arvixel Agent

Arvixel Agent is a Windows desktop AI agent. Bring your own API key, point it at a project folder, and it reads, creates and edits files for you. Every file write shows a diff and waits for your approval before anything is saved.

**Status: Public Beta.** Tested on Windows 10 (x64). Windows 11 has not been tested yet.

## Download

Get the installers from the [Releases page](https://github.com/abdullahbajwa90/arvixel-agent-releases/releases/latest).

- `Arvixel Agent_0.1.0_x64-setup.exe` — 5,251,913 bytes
  SHA256: `E9C2AA23FBC9C44B4FA68DA746721C241EA72027829987FD9DEB3A108C31688A`
- `Arvixel Agent_0.1.0_x64_en-US.msi` — 7,491,584 bytes
  SHA256: `8BB8A5BC6B3D9EC2EA92928BE9D2F059D0FE6EF16D7BE3294BCFFDB987F72B2E`

### Verify your download

In PowerShell, in the folder with the file:

```powershell
Get-FileHash ".\Arvixel Agent_0.1.0_x64-setup.exe" -Algorithm SHA256
```

The result must match the SHA256 above. If it doesn't, don't run the installer and tell us on Discord.

## Install

1. Download the installer and verify its hash (above).
2. Run it. Arvixel Agent is not code-signed yet, so Windows SmartScreen may show a warning for an unrecognized app. If it does, choose **More info**, then **Run anyway**.
3. Open the app, go to **Settings → Providers & Keys**, add a key for a provider, and press **Save & Test**.
4. Add a project folder and start a chat inside it.

## Requirements

- Windows 10 (x64). Windows 11 is untested.
- Your own API key from a supported provider.

## Providers

OpenAI, Anthropic, Gemini, OpenRouter, Groq, Together, Fireworks, DeepSeek, Mistral, Cerebras, and any custom OpenAI-compatible HTTPS endpoint.

**freellmapi** is an optional provider. It is a third-party local service by another developer, not part of Arvixel Agent, and it is not included in the installer. If you want to use it, install it separately; it runs on your own PC at `127.0.0.1:31415`.

Keys are verified with the provider before saving and are stored in Windows Credential Manager, never in app files. Free keys have provider-set rate limits; for heavy use, a paid key gives the best results. No default or recommended model is set — you choose.

**In our testing:** gpt-oss-120b on Groq created and edited files reliably. Free or very small models — Gemini Flash Lite, freellmapi's "auto" router, free OpenRouter models like Nemotron — often failed at file edits, printed raw code or tool markup, or answered identity questions wrongly (Nemotron identified itself as gpt-oss-120b).

## What it can do

- **Modes:** Ask / Approve for me / Full access (file writes always need approval regardless); Work / Plan (Plan does not edit files); Action mode auto/native/text/off (native tool mode is OpenRouter-only for now); Effort auto/minimal/low/medium/high/xhigh/max; Compare mode (an Architect model does the work, a Sentinel model reviews the final reply — Sentinel cannot read files or run tools, and Compare sends two requests).
- **Slash commands:** `/new`, `/rename`, `/plan`. Type `@` to pick a model.
- **Attachments:** up to 5 per message. Images (.png, .jpg, .jpeg, .webp) up to 5 MiB each, resized to 1,568 px. Text files up to 256 KiB each, 512 KiB combined. Free plan cap: 10 images and 20 text files per rolling 8 hours. If a model can't see images, use **Read text** (Windows OCR, needs the matching language pack) or **Describe with** a vision model first.

## Tools

The agent has exactly 7 tools, at most 5 actions per request:

| Tool | Limits |
|---|---|
| `open_target` | — |
| `fetch_url` | 7 s connect / 18 s total timeout, 1 MB download, 12,000 chars returned. Private addresses (127.0.0.1, 192.168.x.x, etc.) are blocked, no redirects followed. Cannot render JavaScript pages. |
| `read_file` | Up to 1 MB file, 7,800 chars returned. |
| `create_file` | Up to 1 MB. Needs your approval. |
| `overwrite_file` | Up to 1 MB. Needs your approval. |
| `edit_file` | 1–64 patches, each must match exactly once. Up to 1 MB. Needs your approval. |
| `run_command` | Runs via `cmd.exe`. Always asks first. 60 s timeout, 32,000-byte output cap. |

Approval cards expire after 180 seconds.

## Privacy

- Keys: Windows Credential Manager, never in app files.
- Chat history: stored locally, encrypted with AES-256-GCM. A history export is plaintext.
- Your prompts, attachments, and file/page contents the agent reads are sent to the provider you selected, under that provider's own policy.
- Secret detection in messages is best-effort, not guaranteed — don't paste keys or passwords.

## Safety

- Every file write shows a diff and needs your approval — in every permission mode, including Full access.
- `run_command` always asks first; a deny-list blocks dangerous commands (e.g. `del`).
- File tools are scoped to the active project folder; sensitive paths like `.env` are blocked.
- These checks run in the app's Rust host, not in the model prompt.

**The deny-list is not an operating system sandbox.** Approved commands run with your Windows account's own permissions — review a command before approving it.

## Known limitations

- Compare mode sends two requests; the Sentinel only reviews the Architect's final reply.
- Actions have an 8,192-token output cap — very long files can fail to generate in one go.
- `fetch_url` cannot render JavaScript-heavy pages.
- Groq may occasionally return `tool_use_failed`; the app retries once automatically.
- Free or very small models may fail at file edits (see "In our testing" above).
- Only Windows 10 (x64) has been tested so far.

## Community & support

- Discord: https://discord.gg/HaYA2v7Pfn
- Email: abdullahbajwaofficial1@gmail.com
- Found a bug? Post it on Discord or email us. Never share your API keys when reporting an issue.

## License

Copyright (c) 2026 Abdullah Bajwa. All rights reserved.