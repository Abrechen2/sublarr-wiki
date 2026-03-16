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

Forced subtitles are intended only for foreign-language dialogue in an otherwise same-language film (e.g. the Elvish scenes in a full-English movie). `SUBLARR_FORCED_PREFERENCE` controls how they are treated in search results.

```
forced_preference: str = "include"  # include | prefer | exclude | only
```

| Option | Value | Behavior |
|--------|-------|---------|
| Disabled | `disabled` | Forced tracks ignored |
| Separate | `separate` | Saved as `.forced.{lang}.ass` |
| Auto | `auto` | Detected from stream metadata |

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Forced Preference | `include` | `SUBLARR_FORCED_PREFERENCE` | How forced subtitles are treated in search results: `include`, `prefer`, `exclude`, `only` |

**Preference values:**

| Value | Effect |
|-------|--------|
| `include` | Forced subtitles are included in search results and scored normally |
| `prefer` | Forced subtitles receive a score bonus (+30) |
| `exclude` | Forced subtitles are filtered out of search results |
| `only` | Only forced subtitles are returned |

## Hearing Impaired Removal

Hearing-impaired (HI) subtitles include on-screen annotations such as `[DOOR SLAMS]` or `[TENSE MUSIC]` for viewers who cannot hear the audio. Two settings control how Sublarr handles them.

```
hi_removal_enabled: bool = False
hi_preference: str = "include"  # include | prefer | exclude | only
```

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| HI Removal | `false` | `SUBLARR_HI_REMOVAL_ENABLED` | Strip `[DOOR SLAMS]` style annotations from subtitles on download |
| HI Preference | `include` | `SUBLARR_HI_PREFERENCE` | How HI subtitles are treated in search results: `include`, `prefer`, `exclude`, `only` |

**Preference values:**

| Value | Effect |
|-------|--------|
| `include` | HI subtitles are included in search results and scored normally (default) |
| `prefer` | HI subtitles receive a score bonus (+30) |
| `exclude` | HI subtitles are filtered out of search results |
| `only` | Only HI subtitles are returned |

## Subtitle Trash

Instead of permanently deleting files, Sublarr moves them to a `.trash` subdirectory.

`SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` (default: `7`) — auto-purge after this many days.

## Stream Removal (Remux)

Stream Removal uses mkvmerge or ffmpeg to remux MKV files, removing embedded subtitle streams and optionally audio streams. Before remuxing, Sublarr creates a backup copy in `remux_trash_dir` so the original file can be recovered if something goes wrong.

```
remux_trash_dir: str = ".sublarr"        # Relative (to media_path) or absolute path for backup trash
remux_backup_retention_days: int = 7     # 0 = keep forever
remux_arr_pause_enabled: bool = True     # Pause Sonarr/Radarr during remux
remux_use_reflink: bool = True           # Use copy-on-write reflinks on Btrfs/XFS for zero-cost backups
```

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Trash directory | `.sublarr` | `SUBLARR_REMUX_TRASH_DIR` | Relative (to `media_path`) or absolute path where pre-remux backups are stored |
| Backup retention | `7` days | `SUBLARR_REMUX_BACKUP_RETENTION_DAYS` | How long backup copies are kept; `0` keeps them forever |
| Pause arr on remux | `true` | `SUBLARR_REMUX_ARR_PAUSE_ENABLED` | Pause Sonarr/Radarr monitoring during remux to prevent them from detecting the file as missing or changed mid-operation |
| Use reflink | `true` | `SUBLARR_REMUX_USE_REFLINK` | Use copy-on-write reflinks on Btrfs/XFS for zero-cost backups (falls back to a regular copy on unsupported filesystems) |

See [Stream Removal](/user-guide/stream-removal) for full documentation including provider configuration and per-series overrides.