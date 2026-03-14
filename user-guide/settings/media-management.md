---
title: Settings — Media Management
description: File naming, importing, and media path configuration
published: true
date: 2026-03-14T19:29:00.458Z
tags: settings
editor: markdown
dateCreated: 2026-03-14T18:04:57.685Z
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab active" href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Media Management

This page covers how Sublarr organises subtitle files, handles existing subtitles, and manages soft-deleted files.

## Root Folder Paths

Root folders define where Sublarr looks for media files. These must match your Sonarr/Radarr library paths and your Docker volume mounts.

| Field | Description |
|-------|-------------|
| **Path** | Absolute path inside the container (e.g. `/media/anime`) |
| **Type** | `TV`, `Movies`, or `Mixed` |
| **Auto-scan** | Whether the folder is included in periodic scans |

### Path Mapping

If Sonarr sees your media at a different path than Sublarr, configure path mappings in **Settings → Sonarr/Radarr**:

Example: remote `/tv` → local `/media/tv`

Environment variable: `SUBLARR_PATH_MAPPING=remote1=local1;remote2=local2`

## Subtitle File Naming

```
{MediaFileName}.{language}.{format}

Show.S01E01.mkv  →  Show.S01E01.de.ass
Movie.2023.mkv   →  Movie.2023.en.srt
Anime.S02E12.mkv →  Anime.S02E12.de.ass
```

Language codes follow ISO 639-1 (2-letter: `en`, `de`, `ja`) by default.

## Subtitle Format Preferences

| Format | Strengths | When to use |
|--------|-----------|-------------|
| **ASS** | Preserves styling, positioning, colours | Anime — strongly recommended |
| **SRT** | Universal compatibility | General TV/movies |

ASS subtitles receive a **+50 scoring bonus** over SRT.

## Existing Subtitle Behaviour

| Scenario | Default behaviour |
|----------|------------------|
| Existing score ≥ new score | Skip — kept as-is |
| New score exceeds by ≥ `upgrade_min_score_delta` | Upgrade — replace |
| New is ASS, existing is SRT, `upgrade_prefer_ass=true` | Always upgrade |
| Item in `upgrade_window_days` | Requires 2× score delta |

| Setting | Default | Env Variable |
|---------|---------|-------------|
| Upgrade enabled | `true` | `SUBLARR_UPGRADE_ENABLED` |
| Minimum score delta | `50` | `SUBLARR_UPGRADE_MIN_SCORE_DELTA` |
| Upgrade window (days) | `7` | `SUBLARR_UPGRADE_WINDOW_DAYS` |
| Prefer ASS upgrade | `true` | `SUBLARR_UPGRADE_PREFER_ASS` |

## Forced Subtitle Handling

| Option | Value | Behavior |
|--------|-------|---------|
| Disabled | `disabled` | Forced tracks ignored |
| Separate | `separate` | Saved as `.forced.{lang}.ass` |
| Auto | `auto` | Detected from stream metadata |

## Hearing Impaired Removal

`SUBLARR_HI_REMOVAL_ENABLED` (default: `false`) — strips `[DOOR SLAMS]` style annotations on download.

## Subtitle Trash

Instead of permanently deleting files, Sublarr moves them to a `.trash` subdirectory.

`SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` (default: `7`) — auto-purge after this many days.

## Stream Removal (Remux)

Removes embedded subtitle streams from MKV files via mkvmerge or ffmpeg.

`SUBLARR_REMUX_USE_REFLINK=true` — use copy-on-write reflinks on Btrfs/XFS for zero-cost backups.