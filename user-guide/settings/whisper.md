---
title: Settings — Whisper
description: Whisper speech-to-text transcription backend configuration for subtitle generation from audio
published: true
date: 2026-03-15
tags: settings
editor: markdown
dateCreated: 2026-03-15
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Whisper

![Settings — Whisper](/img/screen-settings-whisper.png)

## Overview

Whisper is OpenAI's open-source speech recognition model. Sublarr integrates Whisper as a **last-resort subtitle source**: when all configured providers fail to find a subtitle for a media item and the video file has a compatible audio track, Sublarr can transcribe the audio directly and generate a subtitle file.

Transcription is computationally expensive. On CPU, a 24-minute episode takes 5–20 minutes depending on model size and hardware. On a modern GPU, the same episode takes under 2 minutes with the `large` model.

Whisper-generated subtitles are stored alongside provider-downloaded subtitles and are subject to the same upgrade and scoring rules. They are tagged with a `whisper` source flag in the library view.

> [!NOTE]
> Whisper is disabled by default. No transcription runs unless **Auto-transcribe if no subtitle found** is explicitly enabled, or you trigger transcription manually from the Library or Wanted pages.

---

## Whisper Backend

| Setting | Default | Description |
|---------|---------|-------------|
| Backend | `local` | Which Whisper implementation to use. Options: `local` (bundled `openai-whisper` Python package), `faster-whisper` (CTranslate2-based, faster and lower memory), `openai-api` (remote OpenAI-compatible API endpoint) |
| Model size | `medium` | Whisper model to load. Options: `tiny`, `base`, `small`, `medium`, `large`. Larger models are more accurate but require more RAM and are slower on CPU. |
| Device | `auto` | Compute device. `auto` selects CUDA if available, otherwise CPU. Force `cpu` or `cuda` explicitly if auto-detection gives unexpected results. |
| Language | *(empty)* | BCP-47 language code (e.g. `ja`, `en`, `de`). Leave empty for automatic language detection. Detection adds a small overhead per file. |

**Model size reference:**

| Model | VRAM (GPU) | RAM (CPU) | Relative speed | Notes |
|-------|-----------|-----------|---------------|-------|
| `tiny` | ~1 GB | ~1 GB | Fastest | Very low accuracy, mainly for testing |
| `base` | ~1 GB | ~1 GB | Fast | Acceptable for English-only content |
| `small` | ~2 GB | ~2 GB | Moderate | Good balance for English |
| `medium` | ~5 GB | ~5 GB | Slow on CPU | Recommended default |
| `large` | ~10 GB | ~10 GB | Slow on CPU | Best accuracy; required for Japanese/Chinese |

> [!TIP]
> If you run Sublarr on a machine without a dedicated GPU (e.g. an LXC container on a home server), use `faster-whisper` with the `small` or `base` model for acceptable speed on CPU. The `faster-whisper` backend uses 4-bit quantised models and is typically 2–4× faster than the standard `local` backend on the same hardware.

---

## Transcription

| Setting | Default | Description |
|---------|---------|-------------|
| Auto-transcribe if no subtitle found | `false` | Automatically queue a Whisper transcription job when all provider searches for a Wanted item return no results |
| Confidence threshold | `0.7` | Minimum average segment confidence score (0.0–1.0). Transcriptions where the mean per-segment confidence falls below this threshold are discarded and not saved to disk. |

The confidence threshold guards against low-quality transcriptions — for example, music-only segments, heavily accented speech, or audio with significant background noise that Whisper misidentifies as speech. A value of `0.7` discards roughly the lowest 10% of transcription attempts.

To disable the threshold and keep all transcriptions regardless of quality, set it to `0.0`.

---

## OpenAI-Compatible API

These settings are shown only when **Backend** is set to `openai-api`. They allow Sublarr to delegate transcription to a remote server running Whisper behind an OpenAI-compatible `/v1/audio/transcriptions` endpoint — for example, a separate machine with a GPU, a self-hosted [whisper.cpp](https://github.com/ggerganov/whisper.cpp) server, or a cloud provider.

| Setting | Default | Description |
|---------|---------|-------------|
| API URL | *(empty)* | Base URL of the OpenAI-compatible transcription API (e.g. `http://192.168.178.155:8000`). Sublarr appends `/v1/audio/transcriptions` automatically. |
| API Key | *(empty)* | Bearer token sent in the `Authorization` header. Leave empty if the server does not require authentication. |

> [!NOTE]
> The **Model size** setting is passed as the `model` field in the API request body. If the remote server uses a fixed model regardless of this parameter, the setting has no effect but does not cause an error.

**Example local GPU server setup** using `faster-whisper` via the `whisper-asr-webservice` Docker image:

```yaml
services:
  whisper:
    image: onerahmet/openai-whisper-asr-webservice:latest
    environment:
      - ASR_MODEL=large-v3
      - ASR_ENGINE=faster_whisper
    ports:
      - "9000:9000"
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
```

Set **API URL** to `http://<host>:9000` and **Backend** to `openai-api`.

---

## When Transcription Runs

Transcription is triggered in three ways:

1. **Automatic** — `Auto-transcribe if no subtitle found` is enabled, a Wanted search exhausts all providers, and the media file has an audio track that Whisper can process (any common codec: AAC, AC3, DTS, MP3, FLAC).
2. **Manual from Wanted** — Click the **Transcribe** button on any item in the Wanted list, regardless of whether providers have been tried.
3. **Manual from Library** — Open a media item's detail page and click **Generate Subtitle via Whisper** to create an additional subtitle track.

Transcription jobs are queued and run sequentially (one at a time) to avoid memory exhaustion. Queue status is visible on the **Activity** page.

---

## Quality Notes

Whisper accuracy varies considerably by language, audio quality, and model size.

**Recommendations by language:**

| Language | Minimum model | Notes |
|----------|--------------|-------|
| English | `small` | Strong accuracy even at smaller sizes |
| German, French, Spanish | `small` | Good accuracy; `medium` for better punctuation |
| Japanese | `large` | Smaller models struggle with kanji disambiguation and mixed-script output |
| Chinese (Mandarin) | `large` | Similar to Japanese; `large-v3` preferred for Traditional Chinese |
| Arabic, Hindi | `medium` or `large` | Script-heavy languages need larger context windows |

**Common sources of low-quality transcription:**

- **Dual audio tracks** — Whisper receives the first audio track by default. If the first track is the original Japanese and you want English transcription, configure the audio track index in your media profile.
- **Songs and OP/ED segments** — Whisper frequently transcribes lyrics as spoken dialogue. Use Credit Filtering (see [Subtitle Tools](/user-guide/settings/subtitle-tools)) to remove these lines after transcription.
- **Overlapping dialogue** — Scenes with multiple simultaneous speakers are transcribed as a single stream; speaker attribution is not supported.

> [!TIP]
> For anime, consider using Whisper with the Japanese audio track (`language: ja`) combined with Sublarr's LLM Translation to produce translated subtitles when no fansub is available. This pipeline produces better results than transcribing an English dub, because original Japanese audio is typically cleaner and more accurately captured by Whisper's Japanese model weights.
