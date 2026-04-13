---
title: Settings — Media Management
description: File naming, importing, and media path configuration
published: true
date: 2026-04-13
---

# Settings — Media Management

## Subtitle File Naming

Sublarr writes subtitle files alongside your media using this naming pattern:

```
{MediaFileName}.{language}.{format}
```

Examples:
- `Show.S01E01.mkv` → `Show.S01E01.de.ass`
- `Movie.2023.mkv` → `Movie.2023.en.srt`

The language code uses ISO 639-1 (2-letter: `en`, `de`, `ja`) or ISO 639-2 (3-letter: `eng`, `deu`).

### Naming Configuration

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Language Code Format | `iso_639_1` | `SUBLARR_SUBTITLE_LANGUAGE_CODE_FORMAT` | Language code style in filenames: `iso_639_1` (2-letter: `de`) or `iso_639_2` (3-letter: `deu`) |
| Suffix Separator | `dot` | `SUBLARR_SUBTITLE_SUFFIX_SEPARATOR` | Character between filename parts: `dot` (`.de.ass`), `dash` (`-de.ass`), or `underscore` (`_de.ass`) |
| HI Suffix | `hi` | `SUBLARR_SUBTITLE_HI_SUFFIX` | Suffix appended for hearing-impaired subtitles (e.g. `Show.S01E01.en.hi.srt`) |
| Forced Suffix | `forced` | `SUBLARR_SUBTITLE_FORCED_SUFFIX` | Suffix appended for forced subtitles (e.g. `Show.S01E01.en.forced.srt`) |

## Root Folders

Root folders define where Sublarr looks for media files. These must match your Jellyfin/Emby library paths and your Docker volume mounts.

Add root folders under **Settings → Media Management → Root Folders**.

## Scan Settings

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Scan Ignore Patterns | `[]` | `SUBLARR_SCAN_IGNORE_PATTERNS` | JSON array of glob patterns to exclude from scans (e.g. `["**/extras/**", "**/.recycle/**"]`) |
| Min File Size (MB) | `0.0` | `SUBLARR_SCAN_MIN_FILE_SIZE_MB` | Skip media files smaller than this size. Useful for filtering samples and trailers |
| Scan Ignore Languages | `[]` | `SUBLARR_SCAN_IGNORE_LANGUAGES` | JSON array of ISO 639-1 language codes to skip during scan (e.g. `["zh", "ar"]`) |
| Metadata Engine | `auto` | `SUBLARR_SCAN_METADATA_ENGINE` | Engine for reading media metadata: `ffprobe`, `mediainfo`, or `auto` (tries ffprobe first) |
| Metadata Max Workers | `2` | `SUBLARR_SCAN_METADATA_MAX_WORKERS` | Parallel workers for batch metadata scans. Increase on fast storage; decrease on NAS |

> [!TIP]
> Use `scan_ignore_patterns` to exclude directories like extras, samples, or recycle bins from provider searches. This reduces unnecessary API calls.

## Per-Language Score Thresholds

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Score Threshold Per Language | `{}` | `SUBLARR_SCORE_THRESHOLD_PER_LANGUAGE` | JSON object mapping language codes to minimum scores (e.g. `{"de": 80, "fr": 70}`) |

> [!NOTE]
> When a language-specific threshold is set, it overrides the global minimum score for that language. Languages not in the map use the default profile threshold.

## Download Limits

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Max Concurrent Provider Searches | `3` | `SUBLARR_MAX_CONCURRENT_PROVIDER_SEARCHES` | Maximum simultaneous provider search requests |
| Max Subtitle File Size (KB) | `2048` | `SUBLARR_MAX_SUBTITLE_FILE_SIZE_KB` | Reject downloaded subtitles larger than this (ZIP bomb protection) |
| Delay Between Providers (ms) | `0` | `SUBLARR_DOWNLOAD_DELAY_BETWEEN_PROVIDERS_MS` | Milliseconds to wait between provider download attempts. `0` = no delay |
| Gestdown Retry Delay (s) | `1.0` | `SUBLARR_GESTDOWN_RETRY_DELAY_S` | Seconds to wait before retrying after HTTP 423 (Locked) from Gestdown |

## Subtitle Trash

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Trash Retention (days) | `30` | `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` | Days to keep trashed subtitle files before auto-purge. `0` = keep forever |

> [!NOTE]
> When you delete a subtitle from the UI, it is moved to a `.trash` directory next to the media file. After the retention period, trashed files are automatically purged.

## FFmpeg

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| FFmpeg Timeout | `120` | `SUBLARR_FFMPEG_TIMEOUT` | Seconds before a running ffmpeg subtitle extraction is killed |

> [!WARNING]
> If you have very large media files or slow storage, you may need to increase this timeout. The default of 120 seconds is sufficient for most files.

## HI Interjections

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| HI Interjections List | _(empty)_ | `SUBLARR_HI_INTERJECTIONS_LIST` | Newline-separated list of custom hearing-impaired interjection patterns. Empty = use the built-in list (`backend/data/hi_interjections.txt`) |

## Post-Processing Pipeline

Automatic subtitle processing steps that run after every download or extraction.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Common Fixes | `false` | `SUBLARR_AUTO_PROCESS_COMMON_FIXES` | Auto-apply common subtitle fixes (typos, OCR errors, formatting) |
| Common Fixes Config | _(empty)_ | `SUBLARR_AUTO_PROCESS_COMMON_FIXES_CONFIG_JSON` | JSON configuration for common fixes; empty = use defaults |
| HI Removal | `false` | `SUBLARR_AUTO_PROCESS_HI_REMOVAL` | Auto-remove hearing-impaired annotations after download |
| Credit Removal | `false` | `SUBLARR_AUTO_PROCESS_CREDIT_REMOVAL` | Auto-remove ending credits from subtitles |
| Sync Threshold | `60` | `SUBLARR_AUTO_PROCESS_SYNC_THRESHOLD` | Score below which auto-sync is triggered (0–100) |
| Sync Fallback Engine | `ffsubsync` | `SUBLARR_AUTO_PROCESS_SYNC_FALLBACK_ENGINE` | Fallback sync engine if primary fails (`ffsubsync` or `alass`) |
| Auto-Translate (Wanted) | `false` | `SUBLARR_WANTED_AUTO_TRANSLATE` | Auto-translate subtitles during wanted scan |
| Auto-Translate (Webhook) | `true` | `SUBLARR_WEBHOOK_AUTO_TRANSLATE` | Auto-translate after webhook-triggered download |

### Post-Download Command

Execute a custom shell command after each subtitle download.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Post-Processing Enabled | `false` | `SUBLARR_POST_PROCESSING_ENABLED` | Enable post-download command execution |
| Post-Download Command | _(empty)_ | `SUBLARR_POST_DOWNLOAD_COMMAND` | Shell command to run after each subtitle download (60s timeout, non-blocking) |

> [!TIP]
> Available variables in the command: `{subtitle_path}`, `{video_path}`, `{language}`, `{provider}`, `{score}`, `{media_type}`.

## Remux / Stream Removal

Settings for embedded subtitle stream removal from MKV containers after extraction.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Trash Directory | `.sublarr` | `SUBLARR_REMUX_TRASH_DIR` | Relative (to media_path) or absolute path for remux backup storage |
| Backup Retention | `7` | `SUBLARR_REMUX_BACKUP_RETENTION_DAYS` | Days to keep remux backups before auto-purge (0 = keep forever) |
| Use Reflink | `true` | `SUBLARR_REMUX_USE_REFLINK` | Attempt CoW reflink copy (Btrfs/XFS) for zero-cost backups |
| Pause *arr Monitoring | `true` | `SUBLARR_REMUX_ARR_PAUSE_ENABLED` | Pause Sonarr/Radarr folder monitoring during remux to prevent false import triggers |

> [!WARNING]
> Remux operations modify the original MKV file by removing subtitle streams. A backup is always created before modification. Set `remux_backup_retention_days` to `0` to keep backups indefinitely.

## Import Behaviour

- Sublarr **never deletes or modifies media files** — it only creates `.ass` and `.srt` sidecar files (except during remux, which removes embedded subtitle streams with backup)
- Existing subtitles with a higher score than the found subtitle are not overwritten (upgrade threshold configurable)
- Files are written with the same permissions as the media file's directory
