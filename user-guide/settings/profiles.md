---
title: Settings — Profiles
description: Quality profiles, language profiles, and scoring configuration
published: true
date: 2026-03-14T19:27:33.845Z
tags: settings
editor: markdown
dateCreated: 2026-03-14T18:04:59.363Z
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab active" href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Profiles

## Language Profiles

Language Profiles tell Sublarr which subtitle languages to find or translate to, and how to handle each one. Every series or movie in the Library can be assigned exactly one language profile. If no profile is assigned, the global defaults from **Settings → Translation** are used.

### What a Language Profile Controls

| Field | Description |
|-------|-------------|
| **Name** | A human-readable label, e.g. `Anime DE`, `Movies EN` |
| **Target Language** | The language to translate to (ISO 639-1: `de`, `fr`, `es`) |
| **Source Language** | The original subtitle language (usually `en`) |
| **Format Preference** | ASS preferred, SRT only, or any |
| **Translation Backend** | Ollama, DeepL, LibreTranslate, OpenAI-compatible, Google Cloud |
| **Prompt Preset** | Translation style: Anime, General, Documentary |
| **Forced Preference** | Disabled, Separate, or Auto |
| **Upgrade Prefer ASS** | Always replace SRT with ASS when ASS becomes available |

### Creating a Language Profile

1. Go to **Settings → Translation → Language Profiles**
2. Click **Add Profile**
3. Fill in the profile fields
4. Click **Save**

### Profile Lookup Order

1. Series-specific profile (manually set in Library)
2. Tag-assigned profile (from Sonarr/Radarr tags)
3. Global default settings (`SUBLARR_SOURCE_LANGUAGE` / `SUBLARR_TARGET_LANGUAGE`)

### Multi-Language Profiles

One profile can target multiple languages simultaneously. Click **Add Language** in the profile editor to add more target languages. Sublarr searches for and translates to each language independently.

### Tag-Based Automatic Assignment

1. Go to **Settings → Integrations → Tag Profile Mapping**
2. Add mappings such as:
   - Sonarr tag `anime-de` → Profile `Anime DE`
   - Sonarr tag `documentary` → Profile `Documentary FR`
3. Sublarr reads tags from Sonarr/Radarr on every webhook event and assigns the matching profile automatically

## Quality / Score Thresholds

Each profile sets a minimum score (0–100) that a subtitle must reach before it is downloaded.

| Score Range | Meaning |
|-------------|---------|
| 80–100 | High confidence — exact episode hash match |
| 60–79 | Good match — title + season/episode match |
| 40–59 | Weak match — title only |
| < 40 | Rejected — too uncertain |

## Delay Profiles

Delay profiles add a wait time before searching for a newly downloaded episode. Useful for anime where better fansub releases appear 12–24h after air date.

Configure per language profile: **Delay (hours)** — default `0` (no delay).

## Troubleshooting

**Profile not applied to a series:**
- Verify the series is in the Library
- Check **Settings → Integrations → Tag Profile Mapping** for conflicting assignments
- After manually assigning a profile, trigger a manual search to apply it to existing wanted items

**Wrong language downloaded:**
- Verify the target language code is correct (ISO 639-1: `en`, `de`, `ja`, `fr`)
- Check that the provider supports the target language

**Translation not running after download:**
- Check that `SUBLARR_WEBHOOK_AUTO_TRANSLATE=true`
- Verify the translation backend is reachable in [Settings → Translation](/user-guide/settings/translation)