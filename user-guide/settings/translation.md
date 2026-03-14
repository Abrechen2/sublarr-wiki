---
title: Settings — Translation
description: LLM translation backend configuration — Ollama, custom model, pipeline settings
published: true
date: 2026-03-14T19:29:03.156Z
tags: settings
editor: markdown
dateCreated: 2026-03-14T18:05:02.862Z
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab active" href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Translation

Sublarr supports multiple translation backends. For a deep-dive into the translation pipeline itself, see [Translation & LLM](/user-guide/translation-llm).

## Translation Backends

| Backend | Type | Self-Hosted | API Key | Best For |
|---------|------|:-----------:|:-------:|---------|
| **Ollama** | LLM | Yes | No | Full control, GPU-accelerated, offline |
| **DeepL** | API | No | Yes | High-quality European languages |
| **LibreTranslate** | API | Yes/No | Optional | Self-hosted, privacy-focused |
| **OpenAI-compatible** | LLM | Both | Yes | GPT-4, local LLMs |
| **Google Cloud** | API | No | Yes | Broad language support |

## Configuring Ollama

```env
SUBLARR_OLLAMA_URL=http://ollama:11434
SUBLARR_OLLAMA_MODEL=qwen2.5:14b-instruct
```

### Recommended Models

| Model | Size | Best For |
|-------|------|---------|
| `hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M` | 7 GB | Anime EN→DE |
| `qwen2.5:14b-instruct` | ~9 GB | General subtitles |
| `qwen2.5:7b-instruct` | ~5 GB | Low-VRAM systems |

### Custom Anime Model

```bash
ollama pull hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

| Property | Value |
|----------|-------|
| Translation direction | English → German |
| Training data | 75,000 anime subtitle pairs |
| BLEU-1 | 0.281 |
| File size | 7 GB (Q4_K_M) |

See [Translation & LLM](/user-guide/translation-llm#custom-anime-model) for full model details.

## Fallback Chains

1. **Primary:** Ollama (local, fast, free)
2. **Fallback 1:** DeepL (cloud, high quality)
3. **Fallback 2:** LibreTranslate (self-hosted backup)

## Translation Settings

### Language Settings

| Setting | Default | Env Variable |
|---------|---------|-------------|
| Source language | `en` | `SUBLARR_SOURCE_LANGUAGE` |
| Target language | `de` | `SUBLARR_TARGET_LANGUAGE` |
| Source language name | `English` | `SUBLARR_SOURCE_LANGUAGE_NAME` |
| Target language name | `German` | `SUBLARR_TARGET_LANGUAGE_NAME` |

### Batch and Performance Settings

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Batch size | `15` | `SUBLARR_BATCH_SIZE` | Subtitle cues per LLM call |
| Request timeout | `90` | `SUBLARR_REQUEST_TIMEOUT` | Seconds per LLM response |
| Temperature | `0.3` | `SUBLARR_TEMPERATURE` | Lower = more consistent |
| Max retries | `3` | `SUBLARR_MAX_RETRIES` | Retries before marking batch failed |
| Max workers | `4` | `SUBLARR_TRANSLATION_MAX_WORKERS` | Parallel worker threads |

### Webhook Auto-Translation

| Setting | Default | Env Variable |
|---------|---------|-------------|
| Auto-translate on webhook | `true` | `SUBLARR_WEBHOOK_AUTO_TRANSLATE` |
| Auto-translate on extract | `false` | `SUBLARR_WANTED_AUTO_TRANSLATE` |