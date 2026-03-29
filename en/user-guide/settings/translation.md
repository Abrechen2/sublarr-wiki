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
