---
title: Settings — Media Management
description: File naming, importing, and media path configuration
published: true
date: 2026-03-14
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

## Root Folders

Root folders define where Sublarr looks for media files. These must match your Jellyfin/Emby library paths and your Docker volume mounts.

Add root folders under **Settings → Media Management → Root Folders**.

## Import Behaviour

- Sublarr **never deletes or modifies media files** — it only creates `.ass` and `.srt` sidecar files
- Existing subtitles with a higher score than the found subtitle are not overwritten (upgrade threshold configurable)
- Files are written with the same permissions as the media file's directory
