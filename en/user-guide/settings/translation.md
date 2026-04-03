---
title: Settings — Translation
description: LLM translation backend configuration — Ollama, custom model
published: true
date: 2026-03-14
---

# Settings — Translation

> **⚠️ Beta Feature**
> The AI translation feature is experimental and not yet reliable enough for production use. Results vary significantly depending on the model, prompt, and input quality. Use at your own risk.

### Translation Backends

Sublarr supports multiple translation backends. Configure them in Settings > Translation Backends.

| Backend | Type | Self-Hosted | API Key | Best For |
|---------|------|-------------|---------|----------|
| **Ollama** | LLM | Yes | No | Full control, custom prompts, GPU-accelerated |
| **DeepL** | API | No | Yes | High-quality European languages |
| **LibreTranslate** | API | Yes | Optional | Self-hosted, privacy-focused |
| **OpenAI-compatible** | LLM | Both | Yes | GPT-4, local LLMs with OpenAI API |
| **Google Cloud** | API | No | Yes | Broad language support, fast |

**Configuring Ollama (Default):**
1. Install Ollama on your server
2. Pull a model: `ollama pull qwen2.5:14b-instruct`
3. In Sublarr: Settings > Translation Backends > Ollama
4. Enter your Ollama URL and model name
5. Click Test to verify

**Fallback Chains:**
Configure backup backends in case your primary fails. Example:
1. Primary: Ollama (local, fast, free)
2. Fallback 1: DeepL (cloud, high quality)
3. Fallback 2: LibreTranslate (self-hosted backup)

## Ollama — Chat API (V9+)

Sublarr v0.38.0 added support for the Ollama `/api/chat` endpoint alongside
the existing `/api/generate` endpoint.

### Enabling Chat API

In **Settings → Translation Backends → Ollama**, enable the **Chat API (V9+)** checkbox.

| Setting | Default | Description |
|---|---|---|
| Chat API (V9+) | Off | Use `/api/chat` instead of `/api/generate` |
| System Prompt | _(see below)_ | System message injected as the first chat turn |

### Chat vs. Generate — What Changes

| | `/api/generate` (default) | `/api/chat` (V9+) |
|---|---|---|
| Request format | `{"prompt": "..."}` | `{"messages": [{"role": "system", ...}, {"role": "user", ...}]}` |
| System prompt | Embedded in user prompt | Separate `system` message |
| Series context | Not supported | Injected via `{series_context}` placeholder |
| Supported models | All Ollama models | Models with instruction-following (Qwen2.5, Llama 3+) |

### System Prompt & Series Context

The system prompt is the first message sent to the model in chat mode. The
default system prompt instructs the model to translate anime subtitles into
German using informal language (`du`-form).

You can include `{series_context}` in your system prompt as a placeholder.
Sublarr replaces it with the series name and genre at translation time. This
improves consistency for character names and terminology across an episode.

**Example system prompt with series context:**
```
Du bist ein Anime-Untertitel-Übersetzer EN→DE. {series_context}
Übersetze präzise und natürlich. Keine Erklärungen — nur die Übersetzung.
```

When series context is available (e.g. "Serie: Naruto. Genre: Action."), the
placeholder is substituted. When no context is available, the placeholder is
removed cleanly.

### Which Models Benefit from Chat API?

| Model | Recommendation |
|---|---|
| `qwen2.5:14b-instruct` | Chat API recommended — better instruction following |
| `llama3.2:3b` | Chat API recommended |
| Custom `anime-translator-v8-GGUF` | Generate API — fine-tuned on generate-format prompts |
| Custom `anime-translator-v9-GGUF` | Chat API — trained with chat-format prompts |
| DeepSeek-R1 | Chat API recommended |

> **Rule of thumb:** If your model name ends in `-instruct`, use Chat API.
> If you are using the Sublarr custom model, check the model version:
> V8 and earlier → Generate, V9+ → Chat.
