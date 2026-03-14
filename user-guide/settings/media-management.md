---
title: Settings — Media Management
description: Media management settings
published: true
date: 2026-03-14T19:03:35.277Z
tags: 
editor: markdown
dateCreated: 2026-03-14T18:04:57.685Z
---

# Settings — Media Management

This page covers how Sublarr organises subtitle files, handles existing subtitles, and manages soft-deleted files.

## Root Folder Paths

Root folders define where Sublarr looks for media files. These must match your Sonarr/Radarr library paths and your Docker volume mounts.

Add and manage root folders under **Settings → Media Management → Root Folders**.

| Field | Description |
|-------|-------------|
| **Path** | Absolute path inside the container (e.g. `/media/anime`) |
| **Type** | `TV`, `Movies`, or `Mixed` |
| **Auto-scan** | Whether the folder is included in periodic scans |

> **Note:** Every root folder must be accessible inside the Docker container. If your media is at `/mnt/user/media/anime` on the host, mount it as `/media/anime` in the compose file and add `/media/anime` as a root folder in Sublarr.

### Path Mapping (Sonarr/Radarr)

If Sonarr sees your media at a different path than Sublarr (common when each app runs in its own container), configure path mappings:

- Go to **Settings → Sonarr** (or Radarr)
- Add a mapping: **Remote Path** (Sonarr's view) → **Local Path** (Sublarr's view)
- Example: remote `/tv` → local `/media/tv`

Environment variable format: `SUBLARR_PATH_MAPPING=remote1=local1;remote2=local2`

## Subtitle File Naming

Sublarr writes subtitle files alongside your media using this naming convention:

```
{MediaFileName}.{language}.{format}
```

**Examples:**

```
Show.S01E01.mkv      →  Show.S01E01.de.ass
Show.S01E01.mkv      →  Show.S01E01.de.forced.ass
Movie.2023.mkv       →  Movie.2023.en.srt
Anime.S02E12.mkv     →  Anime.S02E12.de.ass
```

Language codes follow ISO 639-1 (2-letter: `en`, `de`, `ja`, `fr`) by default. You can switch to ISO 639-2 (3-letter: `eng`, `deu`, `jpn`) in **Settings → Media Management → Language Code Format**.

## Subtitle File Format Preferences

Sublarr supports both ASS (Advanced SubStation Alpha) and SRT (SubRip Text) formats.

| Format | Strengths | When to use |
|--------|-----------|-------------|
| **ASS** | Preserves styling, positioning, colours, signs/songs typesetting | Anime — strongly recommended |
| **SRT** | Universal compatibility, simpler | General TV/movies, older players |

### ASS Scoring Bonus

The scoring system gives ASS subtitles a **+50 bonus** over SRT subtitles from the same search result set. This means ASS is always preferred when both formats are available for the same release.

Configure the bonus weight in **Settings → Providers → Scoring**.

### Format Preference per Profile

Each [Language Profile](/user-guide/settings/profiles) has a **Format Preference** field:

| Option | Behavior |
|--------|---------|
| **ASS Preferred** | ASS gets +50 scoring bonus; SRT downloaded as fallback |
| **SRT Only** | ASS candidates scored at 0 — effectively excluded |
| **Any** | No format preference applied |

For anime, always use **ASS Preferred**. For general media, **Any** is fine.

## Encoding Settings

Subtitle encoding affects special characters. Sublarr handles encoding automatically:

- **ASS files** are written as UTF-8 with BOM (required by the ASS specification)
- **SRT files** are written as UTF-8 without BOM (broadest player compatibility)
- Incoming SRT files with legacy encodings (Latin-1, Windows-1252) are detected and re-encoded automatically on download

## Existing Subtitle Behaviour

When a subtitle already exists for an episode, Sublarr's behaviour depends on the **Upgrade** settings:

| Scenario | Default behaviour |
|----------|------------------|
| Existing subtitle score ≥ new score | Skip — existing subtitle is kept |
| New subtitle score exceeds existing by ≥ `upgrade_min_score_delta` | Upgrade — replace existing with new |
| New format is ASS, existing is SRT, `upgrade_prefer_ass=true` | Always upgrade regardless of score delta |
| Item in `upgrade_window_days` (recently downloaded) | Requires 2× score delta before upgrading |

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Upgrade enabled | `true` | `SUBLARR_UPGRADE_ENABLED` | Replace low-quality subtitles when better found |
| Minimum score delta | `50` | `SUBLARR_UPGRADE_MIN_SCORE_DELTA` | Score improvement required to trigger upgrade |
| Upgrade window (days) | `7` | `SUBLARR_UPGRADE_WINDOW_DAYS` | Recent subs within this window require 2× delta |
| Prefer ASS upgrade | `true` | `SUBLARR_UPGRADE_PREFER_ASS` | Always upgrade SRT → ASS when ASS is available |

## Forced Subtitle Handling

Forced subtitles contain translations for foreign-language signs, on-screen text, and non-native dialogue. They are common in anime where Japanese text appears on-screen while dialogue is in English.

Configure forced subtitle behaviour globally here, or per-series via the [Language Profile](/user-guide/settings/profiles):

| Option | Env Variable Value | Behavior |
|--------|-------------------|---------|
| Disabled | `disabled` | Forced subtitle tracks are ignored entirely |
| Separate | `separate` | Search for and save forced subs as `.forced.{lang}.ass` |
| Auto | `auto` | Detect forced tracks from stream metadata and handle automatically |

> **Note:** For anime with signs/songs, **Separate** is the recommended setting. It ensures you have both a full dialogue subtitle and a dedicated signs/songs track, which media players can load simultaneously.

## Hearing Impaired Annotations

Sublarr can strip hearing-impaired (HI) annotations from subtitles on download. HI annotations are textual descriptions of non-speech sounds (e.g. `[DOOR SLAMS]`, `♪ upbeat music ♪`).

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| HI Removal enabled | `false` | `SUBLARR_HI_REMOVAL_ENABLED` | Strip HI annotations on download |

This is applied as a post-processing step after download, before writing the final sidecar file.

## Subtitle Trash

Instead of permanently deleting subtitle files, Sublarr moves them to a `.trash` subdirectory within the media folder. Trashed files can be reviewed and restored.

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Retention days | `7` | `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` | Days to keep trashed files before auto-purge. `0` = keep forever |

**Reviewing and restoring trashed subtitles:**
1. Go to **Settings → Media Management → Subtitle Trash** (or **Settings → Automation → Subtitle Trash**)
2. Browse trashed files with their original paths and deletion timestamps
3. Click **Restore** on any file to move it back to its original location
4. Files older than the retention period are automatically deleted on the next cleanup run

## Stream Removal (Remux)

Sublarr can remove embedded subtitle streams from MKV (and other container) files without re-encoding the video. This is done via mkvmerge (preferred for MKV) or ffmpeg (fallback).

| Setting | Default | Env Variable | Description |
|---------|---------|-------------|-------------|
| Trash folder | `.sublarr` | `SUBLARR_REMUX_TRASH_DIR` | Where the original video backup is stored before remux |
| Retention days | `7` | `SUBLARR_REMUX_BACKUP_RETENTION_DAYS` | How long to keep remux backups. `0` = forever |
| Use reflink | `true` | `SUBLARR_REMUX_USE_REFLINK` | Attempt copy-on-write reflink on Btrfs/XFS for zero-cost backups |
| Pause *arr during remux | `true` | `SUBLARR_REMUX_ARR_PAUSE_ENABLED` | Pause Sonarr/Radarr folder monitoring during the remux operation |

Backups are stored in `<trash_dir>/trash/<YYYY-MM-DD>/<file>.<timestamp>.bak`.

> **Warning:** Stream removal is irreversible once the backup expires. Set a long retention period if you are uncertain. mkvtoolnix (mkvmerge) is included in the official Docker image.
