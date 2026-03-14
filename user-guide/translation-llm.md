---
title: Translation & LLM
description: Local LLM translation with Ollama — custom anime model, translation pipeline
published: true
date: 2026-03-14T19:36:16.218Z
tags: llm, translation
editor: markdown
dateCreated: 2026-03-14T18:05:04.509Z
---

# Translation & LLM

<div class="beta-notice"><span class="bn-icon mdi mdi-flask-outline"></span><div><strong>Beta Feature</strong> — LLM-based translation is functional but actively developed. Quality, performance, and configuration options will improve in future releases. For production use with strict quality requirements, consider using <a href="/user-guide/settings/translation">DeepL</a> as a fallback or primary backend.</div></div>

Sublarr supports fully offline subtitle translation using [Ollama](https://ollama.com) and any OpenAI-compatible endpoint. No cloud APIs, no accounts required. This page explains the full translation pipeline, all supported backends, the custom anime model, and how to get the best results.

## Translation Pipeline

Every subtitle request goes through a three-tier pipeline before a translation backend is called. This ensures translation work is never duplicated and upgrades are handled intelligently.

### Tier A — Skip

**Condition:** A target-language subtitle already exists for the episode and its score meets or exceeds the minimum quality threshold for the assigned Language Profile.

**Action:** Nothing happens. The existing subtitle is kept as-is.

### Tier B — Upgrade

**Condition:** A target-language subtitle exists, but one of the following is true:
- Its score is below the upgrade minimum score delta compared to a newly found subtitle
- It is SRT and a higher-quality ASS subtitle has become available (when `upgrade_prefer_ass=true`)
- It falls within the upgrade window (recently downloaded) and the score delta exceeds 2× the threshold

**Action:** The new subtitle is downloaded from the provider and re-translated using the current translation backend. The old file is moved to the Subtitle Trash before being replaced.

### Tier C — Full

**Condition:** No target-language subtitle exists for the episode.

**Action:** Full pipeline runs:
1. Query all enabled providers for the target language
2. Score and rank all results
3. Download the highest-scoring result
4. Apply HI removal if configured
5. Send through the translation backend
6. Write the translated sidecar file
7. Notify configured media servers to rescan

> **Note:** Within each tier, the translation call itself sends subtitle cues in batches (default: 15 cues per LLM call) to stay within the model's context window. Batch size is configurable via `SUBLARR_BATCH_SIZE`.

## Supported Backends

### Ollama (Recommended) <span class="badge-beta">Beta</span>

<div class="beta-notice"><span class="bn-icon mdi mdi-flask-outline"></span><div><strong>Beta</strong> — LLM translation via Ollama is the primary translation method in Sublarr but remains in active development. Translation accuracy depends heavily on the chosen model. Results may vary by language pair and content type.</div></div>

Self-hosted LLM inference. Runs on your server with no external dependencies. Supports GPU acceleration for fast translation.

```env
SUBLARR_OLLAMA_URL=http://ollama:11434
SUBLARR_OLLAMA_MODEL=qwen2.5:14b-instruct
```

**Recommended models:**

| Model | Size | Quality | Speed | Best For |
|-------|------|---------|-------|---------| 
| `hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M` | 7 GB | Excellent (anime) | Fast | Anime EN→DE |
| `qwen2.5:14b-instruct` | ~9 GB | Very good | Medium | General subtitles |
| `qwen2.5:7b-instruct` | ~5 GB | Good | Fast | Low-VRAM systems |
| `gemma3:12b` | ~8 GB | Good | Medium | Alternative general model |

### OpenAI-Compatible

Any server that implements the OpenAI Chat Completions API works as a backend. This includes:

- **OpenAI** (GPT-4o, GPT-4o-mini)
- **Anthropic** (via compatible proxy)
- **vLLM** (self-hosted, high-throughput)
- **LM Studio** (desktop LLM runner)
- **Groq** (cloud, very fast inference)

```env
OPENAI_API_KEY=sk-...
OPENAI_BASE_URL=https://api.openai.com/v1
```

### DeepL

High-quality translation for European languages. Requires a DeepL API key (free tier available). Best used as a fallback backend or primary for languages where LLM translation quality lags (Polish, Romanian, Portuguese, etc.).

### LibreTranslate

Open-source, self-hostable translation API. Quality is lower than LLM or DeepL for most language pairs but runs fully offline.

### Google Cloud Translation

Cloud-based, broad language coverage. Requires a Google Cloud project and API key.

## Custom Anime Model <span class="badge-beta">Beta</span>

<div class="beta-notice"><span class="bn-icon mdi mdi-flask-outline"></span><div><strong>Beta</strong> — The custom anime translation model is an experimental fine-tune. While it outperforms general-purpose models on anime EN→DE, it has not been validated across all anime genres and styles. BLEU-1 score of 0.281 indicates good but not perfect translation quality. Use the <strong>Anime prompt preset</strong> and build a <strong>Glossary</strong> to improve consistency.</div></div>

Sublarr ships a fine-tuned anime subtitle translation model — the first publicly available GGUF model trained specifically on anime subtitles.

### Model Details

| Property | Value |
|----------|-------|
| Name | `anime-translator-v6` |
| Translation direction | English → German |
| Training data | OPUS OpenSubtitles v2018 |
| Training pairs | 75,000 anime subtitle pairs |
| BLEU-1 score | 0.281 |
| Quantisation | Q4_K_M GGUF |
| File size | 7 GB |
| HuggingFace | [huggingface.co/Sublarr](https://huggingface.co/Sublarr) |

The model was trained exclusively on anime subtitle dialogue, making it significantly better at:

- Character name consistency across episodes
- Anime-specific speech patterns and honorifics
- Onomatopoeia and sound effects
- Short, punchy dialogue lines (anime subtitles are typically shorter than movie subtitles)

### Pulling the Model

```bash
ollama pull hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

Requires Ollama 0.3+ and approximately 7 GB of disk space. Runs on consumer GPUs (8 GB VRAM sufficient for Q4_K_M).

### Activating the Model

```env
SUBLARR_OLLAMA_MODEL=hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

Or set it in **Settings → Translation Backends → Ollama → Model name** in the UI.

## How to Switch Models

1. Pull the new model on your Ollama server:
   ```bash
   ollama pull <model-name>
   ```
2. In Sublarr, go to **Settings → Translation Backends → Ollama**
3. Change the **Model** field to the new model name
4. Click **Test** to verify the connection and model is loaded
5. Click **Save**

All new translation jobs will use the updated model. Existing completed translations are not re-done automatically — you can re-translate specific episodes from the [Library](/user-guide/library) Series Detail view.

## Fallback Chains

If your primary backend is unavailable, Sublarr tries fallbacks in order:

1. **Primary:** Ollama (local, fast, no cost)
2. **Fallback 1:** DeepL (cloud, high quality, per-character cost)
3. **Fallback 2:** LibreTranslate (self-hosted backup)

Configure fallback chains in **Settings → Translation Backends**. If all backends fail, the translation job is marked as `failed` and can be retried from the [Activity](/user-guide/activity) page once the backend is available again.

## Performance Tips

### GPU Acceleration

Ollama automatically uses your GPU when available. For best performance:

- Use a model that fits entirely in VRAM (avoids slow CPU offloading)
- Q4_K_M quantisation offers the best quality-to-size trade-off for 8 GB VRAM GPUs
- Set `SUBLARR_TRANSLATION_MAX_WORKERS=1` when running a large model on a shared GPU to avoid OOM errors

### Batch Size Tuning

| Batch size | Effect |
|-----------|--------|
| 5–10 | Fewer cues per call, lower context usage, more API calls, slower overall |
| 15 (default) | Balanced — works for most models with a 4k context window |
| 20–30 | Faster for large context models (32k+); may cause truncation on small models |

If you see garbled or cut-off translations, reduce the batch size.

### Concurrency

`SUBLARR_TRANSLATION_MAX_WORKERS` controls how many translation jobs run in parallel:

- Default: `4` — suitable for a dedicated server
- Set to `1`–`2` on memory-constrained systems or when running a large model

## Prompts and Prompt Presets

Sublarr generates translation prompts automatically based on the source and target language names. You can customise this:

- **Prompt Presets** — predefined prompt styles (Anime, General, Documentary) selectable per Language Profile
- **Custom Prompt Template** — fully replace the auto-generated prompt via `SUBLARR_PROMPT_TEMPLATE`

## Glossary Support

For series with recurring proper nouns (character names, place names, special terms), Sublarr's AI Glossary Builder can extract and manage translation terms:

- Go to a series in the [Library](/user-guide/library) and open the **Glossary** tab
- Click **Build Glossary** to extract terms from existing subtitles automatically
- Review and approve suggested terms
- Approved terms are injected into the translation prompt for that series

## Troubleshooting

**Translation quality is poor:**
- Switch to the custom anime model (`hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M`) for anime EN→DE
- Try a larger model (14b vs 7b parameters)
- Reduce temperature to `0.1`–`0.2` for more consistent output
- Check the prompt preset — use the Anime preset for anime content
- Build a glossary for recurring character names

**Translation is very slow:**
- Verify Ollama is using GPU (check `ollama ps` — it should show VRAM usage)
- Reduce batch size if the model is repeatedly timing out
- Consider switching to a smaller quantised model (Q4_K_M is the recommended quality tier)

**Ollama connection fails:**
- Verify the URL in `.env` matches how Ollama is reachable from Sublarr's container
- On Docker Compose: use the service name (`http://ollama:11434`), not `localhost`
- Check that the model is pulled: `ollama list`
- Check Ollama container logs: `docker logs ollama`