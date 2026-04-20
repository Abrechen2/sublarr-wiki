---
title: Settings — Translation
description: LLM translation backend configuration — Ollama, custom model
published: true
date: 2026-04-13
---

# Settings — Translation

> [!NOTE]
> Translation quality varies by model and language pair. For anime (Japanese → German), models like `qwen2.5:14b-instruct` work well. Test on a few episodes before running batch translations.

## Available Backends (v0.70.0-beta — Lingarr parity)

Sublarr supports **12 translation backends**. Configure each in **Settings → Translation**; the pipeline picks the active one per translation job. Cost tracking, live queue dashboard, per-backend concurrency control, and context-windowing (lookback/lookahead cues for coherence) are shared across all backends.

**LLM backends** (token-priced):

| Backend | Endpoint | Default Model | Price (per 1M tokens) |
|---------|----------|---------------|-----------------------|
| Ollama | local | `qwen2.5:14b-instruct` | free |
| OpenAI-Compatible | any | user-chosen | varies |
| Anthropic Claude | `api.anthropic.com` | `claude-3-5-sonnet-20241022` | $3.00 / $15.00 |
| Google Gemini | `generativelanguage.googleapis.com` | `gemini-1.5-flash` | $0.075 / $0.30 |
| DeepSeek | `api.deepseek.com` | `deepseek-chat` | $0.27 / $1.10 |
| Mistral | `api.mistral.ai` | `mistral-large-latest` | $2.00 / $6.00 |
| OpenAI ChatGPT | `api.openai.com` | `gpt-4o-mini` | $0.15 / $0.60 |

**Character-priced backends:**

| Backend | Default Price (per 1M chars) |
|---------|------------------------------|
| DeepL | $20.00 (Free API: $0) |
| Google Cloud Translation | $20.00 |
| LibreTranslate | self-hosted = free |
| Azure Translator | $10.00 |
| MyMemory | free tier with optional email |

The Queue Dashboard in Settings → Translation shows live job progress, recent completions, backend stats, and costs. Every job writes an audit row to `translation_events` for long-term cost analysis.

## Feature Gate

Translation must be explicitly enabled before any translation jobs can run.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Translation Enabled | `false` | `SUBLARR_TRANSLATION_ENABLED` | Master switch for the translation system. Must be set to `true` before any translation can occur |

> [!NOTE]
> When `translation_enabled` is `false`, all translation-related UI elements are hidden or disabled. Enable it in **Settings → Translation** to unlock the full translation pipeline.

## Language Settings

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Source Language | `en` | `SUBLARR_SOURCE_LANGUAGE` | ISO 639-1 code of the source subtitle language |
| Target Language | `de` | `SUBLARR_TARGET_LANGUAGE` | ISO 639-1 code of the target translation language |
| Source Language Name | `English` | `SUBLARR_SOURCE_LANGUAGE_NAME` | Human-readable source language name (used in LLM prompts) |
| Target Language Name | `German` | `SUBLARR_TARGET_LANGUAGE_NAME` | Human-readable target language name (used in LLM prompts) |

## LLM Backend Settings

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Ollama URL | `http://localhost:11434` | `SUBLARR_OLLAMA_URL` | Ollama base URL — infrastructure endpoint, set in `.env` |
| Ollama Model | `qwen2.5:14b-instruct` | `SUBLARR_OLLAMA_MODEL` | Model name for translation |
| Prompt Template | _(empty)_ | `SUBLARR_PROMPT_TEMPLATE` | Custom prompt template. Empty = auto-generated from language names |
| Batch Size | `15` | `SUBLARR_BATCH_SIZE` | Number of subtitle cues sent per LLM request |
| Request Timeout | `90` | `SUBLARR_REQUEST_TIMEOUT` | LLM request timeout in seconds |
| Temperature | `0.3` | `SUBLARR_TEMPERATURE` | LLM sampling temperature. Lower = more consistent, higher = more creative |
| Max Retries | `3` | `SUBLARR_MAX_RETRIES` | Maximum retry attempts on LLM failure before giving up |

> [!TIP]
> For the best anime subtitle quality, use the custom fine-tuned model: `hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M` (see [HuggingFace](https://huggingface.co/Sublarr)). GGUF is a compressed model format that runs on CPU or GPU. Q4_K_M is a good balance of speed and quality.

## Worker & Performance

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Translation Max Workers | `4` | `SUBLARR_TRANSLATION_MAX_WORKERS` | Parallel worker threads in the job queue thread pool. Increase for higher throughput; decrease on memory-constrained systems |

> [!NOTE]
> The `translation_max_workers` setting applies to the in-process `MemoryJobQueue`. With Redis+RQ, scale workers via `docker compose ... --scale rq-worker=N` instead.

## Episode Context

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Use Episode Context | `false` | `SUBLARR_TRANSLATION_USE_EPISODE_CONTEXT` | Include series/episode metadata in the translation prompt for improved consistency |
| Context Episodes | `1` | `SUBLARR_TRANSLATION_CONTEXT_EPISODES` | Number of surrounding episodes to include as context |
| Series Glossary Auto | `false` | `SUBLARR_TRANSLATION_SERIES_GLOSSARY_AUTO` | Automatically generate and inject a per-series glossary into translation prompts |

> [!TIP]
> Enable episode context for long-running series where character names and terminology must stay consistent across episodes. This increases prompt size and may slow down translation slightly.

### Translation Backends

Sublarr supports multiple translation backends. Configure them in Settings > Translation Backends.

| Backend | Type | Self-Hosted | API Key | Best For |
|---------|------|-------------|---------|----------|
| **Ollama** | LLM | Yes | No | Full control, custom prompts, GPU-accelerated |
| **DeepL** | API | No | Yes | High-quality European languages |
| **LibreTranslate** | API | Yes | Optional | Self-hosted, privacy-focused |
| **OpenAI-compatible** | LLM | Both | Yes | GPT-4, local LLMs with OpenAI API |
| **Google Cloud** | API | No | Yes | Broad language support, fast |

> [!TIP]
> Most users should use the default Ollama settings. Only change these if you have a specific model requirement.

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
