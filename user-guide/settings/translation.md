---
title: Settings — Translation
description: LLM translation backend configuration — Ollama, custom model, pipeline settings
published: true
date: 2026-03-14T19:40:14.713Z
tags: settings
editor: markdown
dateCreated: 2026-03-14T18:05:02.862Z
---

<div class="settings-tabs">
  <a class="settings-tab" href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
  <a class="settings-tab" href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
  <a class="settings-tab" href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
  <a class="settings-tab" href="/user-guide/settings/providers"><span class="mdi mdi-cloud-download-outline"></span> Providers</a>
  <a class="settings-tab active" href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
  <a class="settings-tab" href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Translation

Sublarr supports multiple translation backends. This page covers how to configure each one and the tuning settings that control how translation jobs are processed. For a deep-dive into the translation pipeline itself, see [Translation & LLM](/user-guide/translation-llm).

## Translation Backends

| Backend | Type | Self-Hosted | API Key Required | Best For |
|---------|------|:-----------:|:----------------:|---------|| **Ollama** | LLM | Yes | No | Full control, custom prompts, GPU-accelerated, offline |
| **DeepL** | API | No | Yes | High-quality European languages |
| **LibreTranslate** | API | Yes/No | Optional | Self-hosted, privacy-focused |
| **OpenAI-compatible** | LLM | Both | Yes | GPT-4, local LLMs with OpenAI API |
| **Google Cloud** | API | No | Yes | Broad language support, fast |

Configure backends in **Settings → Translation Backends**. Each backend can be independently enabled or disabled per [Language Profile](/user-guide/settings/profiles).

## Configuring Ollama <span class="badge-beta">Beta</span>

<div class="beta-notice">
  <span class="bn-icon mdi mdi-flask-outline"></span>
  <div><strong>Beta</strong> — LLM-based translation is functional and used in production, but the pipeline, prompt system, and backend configuration are actively developed. Expect improvements and occasional breaking changes between versions.</div>
</div>

Ollama is the default and recommended backend for self-hosted deployments. It runs LLMs locally with no cloud dependency.

### Basic Setup

1. Install and run Ollama on your server (or as a Docker service)
2. Pull a model:

```bash
ollama pull qwen2.5:14b-instruct
```

3. In Sublarr: **Settings → Translation Backends → Ollama**
4. Set the Ollama URL and model name
5. Click **Test** to verify the connection

> **Note:** The Ollama URL is an infrastructure setting — it should be set in `.env` rather than the UI, because the URL is a network endpoint that changes per deployment:
> ```env
> SUBLARR_OLLAMA_URL=http://ollama:11434
> ```

### Custom Anime Translation Model <span class="badge-beta">Beta</span>

<div class="beta-notice">
  <span class="bn-icon mdi mdi-flask-outline"></span>
  <div><strong>Beta</strong> — The custom anime translation model is an experimental fine-tune. BLEU-1 0.281 indicates good but not perfect quality. EN→DE only. Use a general-purpose model for other language pairs.</div>
</div>

Sublarr provides a fine-tuned anime subtitle translation model trained specifically on anime dialogue. It consistently outperforms general-purpose models for EN → DE anime translation.

```bash
ollama pull hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

Configure it as the active model:

```env
SUBLARR_OLLAMA_MODEL=hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

Or set it in **Settings → Translation Backends → Ollama → Model name**.

| Property | Value |
|----------|-------|
| Translation direction | English → German |
| Training data | OPUS OpenSubtitles v2018, 75,000 anime subtitle pairs |
| BLEU-1 | 0.281 |
| File size | 7 GB (Q4_K_M quantisation) |
| HuggingFace | [huggingface.co/Sublarr](https://huggingface.co/Sublarr) |

See [Translation & LLM](/user-guide/translation-llm#custom-anime-model) for full model details.

### Fallback Chains

Configure backup backends in case your primary fails. Fallbacks are tried in order:

1. **Primary:** Ollama (local, fast, free)
2. **Fallback 1:** DeepL (cloud, high quality)
3. **Fallback 2:** LibreTranslate (self-hosted backup)

If the primary backend is unavailable (e.g. Ollama server down), Sublarr automatically tries the next backend in the chain. If all backends fail, the translation job is marked as failed and can be retried from the [Activity](/user-guide/activity) page.

## Translation Pipeline Overview

Every subtitle goes through a three-tier pipeline before the translation backend is called:

| Tier | Condition | Action |
|------|-----------|--------|
| **A — Skip** | Target-language subtitle already exists and meets the minimum score | Do nothing |
| **B — Upgrade** | Existing subtitle exists but is below the upgrade threshold or is SRT when ASS is available | Re-download and re-translate |
| **C — Full** | No subtitle exists | Download from providers, then translate |

For the full pipeline explanation, see [Translation & LLM → Translation Pipeline](/user-guide/translation-llm#translation-pipeline).

## Translation Settings

### Language Settings

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Source language | `en` | `SUBLARR_SOURCE_LANGUAGE` | Language code of the source subtitle |
| Target language | `de` | `SUBLARR_TARGET_LANGUAGE` | Default target language code |
| Source language name | `English` | `SUBLARR_SOURCE_LANGUAGE_NAME` | Human-readable name used in prompts |
| Target language name | `German` | `SUBLARR_TARGET_LANGUAGE_NAME` | Human-readable name used in prompts |

These are global defaults. [Language Profiles](/user-guide/settings/profiles) can override them per series.

### Batch and Performance Settings

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Batch size | `15` | `SUBLARR_BATCH_SIZE` | Subtitle cues per LLM call. Larger = faster but may exceed context window |
| Request timeout | `90` | `SUBLARR_REQUEST_TIMEOUT` | Seconds to wait for a single LLM response before retrying |
| Temperature | `0.3` | `SUBLARR_TEMPERATURE` | LLM sampling temperature. Lower = more consistent, higher = more creative |
| Max retries | `3` | `SUBLARR_MAX_RETRIES` | How many times to retry a failed LLM call before marking the batch failed |
| Max workers | `4` | `SUBLARR_TRANSLATION_MAX_WORKERS` | Parallel worker threads in the translation job queue |

**Tuning guidance:**

- Increase `batch_size` (to 20–30) if your model has a large context window and you want faster throughput
- Decrease `batch_size` (to 5–10) if you get truncated or garbled responses from the model
- Increase `max_workers` for higher throughput on a powerful GPU server; decrease on memory-constrained systems
- Set `temperature` to `0.1`–`0.2` for highly consistent output (less creative, better for translation)

### Prompt Templates

Sublarr generates prompts automatically from the source/target language names. You can override this with a custom template:

```env
SUBLARR_PROMPT_TEMPLATE=
```

Leave empty to use the auto-generated prompt. Custom templates support these variables: `{source_language}`, `{target_language}`, `{batch}`.

## Quality Post-Processing

After translation, Sublarr applies optional post-processing steps:

| Feature | Setting | Description |
|---------|---------|-------------|
| HI Removal | `SUBLARR_HI_REMOVAL_ENABLED` | Strip hearing-impaired annotations `[DOOR SLAMS]` from subtitles |
| Credit filtering | `SUBLARR_CREDIT_THRESHOLD_SEC` | Detect and remove staff-credits-only lines near the end of the file |
| OP/ED detection | `SUBLARR_OP_WINDOW_SEC` | Identify opening/ending regions for analysis (does not modify file) |

## Language Profiles

Language Profiles extend per-backend translation settings to a per-series level. See the dedicated [Language Profiles (Settings → Profiles)](/user-guide/settings/profiles) page for full documentation.

## Webhook Auto-Translation

Control whether translation is triggered automatically after subtitle download:

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Auto-translate on webhook | `true` | `SUBLARR_WEBHOOK_AUTO_TRANSLATE` | Automatically translate after a Sonarr/Radarr webhook download |
| Auto-translate on extract | `false` | `SUBLARR_WANTED_AUTO_TRANSLATE` | Automatically translate after embedded subtitle extraction |