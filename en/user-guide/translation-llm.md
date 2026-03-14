---
title: Translation & LLM
description: Local LLM translation with Ollama — custom anime model, translation pipeline
published: true
date: 2026-03-14
---

# Translation & LLM

Sublarr supports fully offline subtitle translation using [Ollama](https://ollama.com). No cloud APIs, no accounts required.

## Custom Anime Model

Sublarr ships a fine-tuned anime translation model trained on 75,000 subtitle pairs:

```bash
ollama pull hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

| Property | Value |
|----------| ------|
| Direction | English → German |
| Training data | OPUS OpenSubtitles v2018, 75k anime subtitle pairs |
| BLEU-1 | 0.281 |
| Size | 7 GB (Q4_K_M GGUF) |
| Config key | `SUBLARR_OLLAMA_MODEL` |

## Configuring Ollama

Set in `.env` or Settings → Translation:

```env
SUBLARR_OLLAMA_URL=http://ollama:11434
SUBLARR_OLLAMA_MODEL=hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

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
