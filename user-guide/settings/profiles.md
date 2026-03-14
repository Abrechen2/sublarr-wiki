---
title: Settings — Profiles
description: Language profile settings — create and manage translation profiles
published: true
date: 2026-03-14T19:01:56.749Z
tags: 
editor: markdown
dateCreated: 2026-03-14T18:04:59.363Z
---

# Settings — Profiles

## Language Profiles

Language Profiles are the core mechanism that tells Sublarr which subtitle languages to find or translate to, and how to handle each one. Every series or movie in the Library can be assigned exactly one language profile. If no profile is assigned, the global defaults from **Settings → Translation** are used.

### What a Language Profile Controls

| Field | Description |
|-------|-------------|
| **Name** | A human-readable label, e.g. `Anime DE`, `Movies EN`, `Documentary FR` |
| **Target Language** | The language to translate to or download in (ISO 639-1 code, e.g. `de`, `fr`, `es`) |
| **Source Language** | The original subtitle language to translate from (usually `en`) |
| **Format Preference** | ASS preferred, SRT only, or any — affects provider scoring |
| **Translation Backend** | Which backend to use (Ollama, DeepL, LibreTranslate, OpenAI-compatible, Google Cloud) |
| **Prompt Preset** | Which translation style to apply (Anime, General, Documentary, etc.) |
| **Forced Preference** | How to handle forced subtitle tracks (Disabled, Separate, Auto) |
| **Upgrade Prefer ASS** | Always replace SRT with ASS when ASS becomes available |

### Creating a Language Profile

1. Go to **Settings → Translation → Language Profiles**
2. Click **Add Profile**
3. Fill in the profile fields (see table above)
4. Click **Save**

### Editing or Deleting a Profile

- Click the pencil icon on any profile row to edit it
- Click the trash icon to delete it — series assigned to this profile will fall back to the global default
- Changes to a profile take effect on the next scan cycle or webhook event; existing subtitles are not retroactively changed

### Multi-Language Profiles

One profile can target multiple languages simultaneously. For example, a profile that downloads both German and French subtitles:

1. In the profile editor, click **Add Language** to add more target languages
2. Sublarr searches for and translates to each language independently
3. Per-language settings (translation backend, prompt preset) can be customised per entry

### Assigning Profiles to Series

**Manual assignment:**
1. Go to the [Library](/user-guide/library)
2. Click on a series to open the Series Detail view
3. Select a Language Profile from the **Profile** dropdown
4. Click **Save**

**Tag-based automatic assignment** (requires Sonarr/Radarr integration):
1. Go to **Settings → Integrations → Tag Profile Mapping**
2. Add mappings such as:
   - Sonarr tag `anime-de` → Profile `Anime DE`
   - Sonarr tag `documentary` → Profile `Documentary FR`
3. Save
4. Sublarr reads tags from Sonarr/Radarr on every webhook event and assigns the matching profile automatically

### Profile Lookup Order

When processing a subtitle request, Sublarr resolves which profile to use in this priority order:

1. Series-specific profile (manually set in Library)
2. Tag-assigned profile (from Sonarr/Radarr tags, via Tag Profile Mapping)
3. Global default settings (`SUBLARR_SOURCE_LANGUAGE` / `SUBLARR_TARGET_LANGUAGE`)

### Translation Pipeline per Profile

Once a profile is resolved, Sublarr processes each subtitle request through this pipeline:

1. **Search** — query all enabled providers for the target language
2. **Score** — rank results by format, file size, hash match, uploader trust
3. **Download** — fetch the best-scoring result
4. **HI Removal** *(if enabled in the profile)* — strip hearing-impaired markers
5. **Translate** — call the configured translation backend with the configured prompt preset
6. **Quality Score** *(if enabled)* — LLM scores each translated line; low-confidence lines trigger retry
7. **Save** — write `.{lang}.ass` or `.{lang}.srt` next to the video file
8. **Media Server Refresh** — trigger a library rescan on all configured media servers

## Quality / Score Thresholds

Each profile sets a minimum score (0–100) that a subtitle must reach before it is downloaded.

| Score Range | Meaning |
|-------------|---------|
| 80–100 | High confidence — exact episode hash match or release name match |
| 60–79 | Good match — title + season/episode match |
| 40–59 | Weak match — title only |
| < 40 | Rejected — too uncertain, will not be downloaded |

How scores are calculated is documented in [Settings → Providers → Scoring](/user-guide/settings/providers#scoring).

### Format Scoring Interaction

Profiles interact with the scoring system:

- **ASS Preferred** — ASS candidates get a +50 bonus in scoring over SRT candidates
- **SRT Only** — ASS candidates are scored at 0 (effectively excluded from results)
- **Upgrade Prefer ASS** — triggers a SRT → ASS upgrade regardless of score delta, whenever ASS becomes available

For anime, always use **ASS Preferred** with **Upgrade Prefer ASS** enabled. This ensures you always get styled fansub subtitles when available.

## Delay Profiles

Delay profiles add a wait time before searching for a newly downloaded episode, allowing better subtitle releases to appear first. This is useful for newly aired episodes where only machine-translated or low-quality subtitles are available immediately after air date.

Configure per language profile: **Delay (hours)** — default `0` (no delay).

**Example:** Set a 12-hour delay for anime series. By the time the search runs, fansub groups have usually released a properly timed and styled ASS file, resulting in much better quality than searching at the moment of download.

## Troubleshooting

**Profile not applied to a series:**
- Verify the series is in the Library (Sublarr must have scanned it first)
- Check **Settings → Integrations → Tag Profile Mapping** for conflicting tag assignments
- After manually assigning a profile, trigger a manual search to apply it to existing wanted items

**Wrong language downloaded:**
- Verify the target language code is correct (ISO 639-1: `en`, `de`, `ja`, `fr`, etc.)
- Check that the provider supports the target language (not all providers cover all languages)

**Translation not running after download:**
- Check that `SUBLARR_WEBHOOK_AUTO_TRANSLATE=true`
- Verify the translation backend is configured and reachable in [Settings → Translation](/user-guide/settings/translation)
- Check the [Activity → Translation Jobs](/user-guide/activity#translation-jobs-tab) tab for queued or failed jobs
