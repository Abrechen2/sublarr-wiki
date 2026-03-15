---
title: Stream Removal (Remux)
description: Permanently remove embedded subtitle or audio streams from MKV files using mkvmerge.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# Stream Removal (Remux)

Remove unwanted embedded subtitle or audio streams from MKV files using mkvmerge — without re-encoding the video.

## Overview

Many MKV files ship with a large number of embedded subtitle tracks — multiple languages, multiple formats (PGS, ASS, SRT), forced tracks, hearing-impaired tracks, and sign/song tracks. While having options is useful, too many embedded tracks cause problems in practice:

- Jellyfin and Emby may select the wrong subtitle track by default.
- Media servers sometimes show a confusing track list in the player.
- Embedded tracks can conflict with sidecar subtitle files managed by Sublarr.

Stream Removal uses **mkvmerge** to remux the file, copying all desired streams into a new container and simply omitting the streams you want to remove. No re-encoding occurs — the video, audio, and kept subtitle streams are copied byte-for-byte.

## When to Use

- Jellyfin or Emby defaults to an embedded subtitle track instead of your Sublarr-managed sidecar file.
- A video file has 8+ embedded subtitle tracks and you want to keep only one or none.
- You want to enforce a sidecar-only subtitle workflow and eliminate all embedded tracks.
- A specific embedded audio track is problematic and you want to drop it.

## How It Works

1. Sublarr reads the track list from the MKV file using `mkvmerge --identify`.
2. You select which tracks to remove via the Tracks panel in the Library.
3. Sublarr backs up the original file to `/config/trash/<YYYY-MM-DD>/<path>` (same trash system as [Subtitle Trash](subtitle-trash.md)).
4. Sublarr runs `mkvmerge` to produce a new MKV file that includes all streams except the removed ones.
5. The new file replaces the original at the same path.

The remux process is lossless — the video stream is not touched. Audio and subtitle streams that are kept are copied without modification.

> [!WARNING]
> Stream removal modifies the video file. Verify the result in your media player before the backup retention period expires (default 7 days). After that period, the original is purged from trash and the operation cannot be undone.

## Triggering Removal

1. Navigate to **Library**, open the series, and find the episode.
2. Click the **Tracks** panel on the episode row to expand the track list. This shows all embedded video, audio, and subtitle streams with their track IDs, language codes, and names.
3. Click **Remove** next to the track you want to eliminate.
4. Confirm the warning dialog. The remux job is queued immediately.

The remux job runs in the background. A spinner is shown on the episode row while it is running. Duration depends on file size — a typical 350 MB episode file takes 10–30 seconds to remux.

## Supported Formats

| Format | Supported |
|--------|-----------|
| MKV (.mkv) | Yes |
| MP4 (.mp4) | No |
| AVI (.avi) | No |
| TS (.ts) | No |

Stream removal is limited to MKV files because mkvmerge only operates on Matroska containers. MP4 and other formats are not supported. For non-MKV files, consider remuxing to MKV first using a tool like HandBrake or FFmpeg, then use Sublarr's stream removal.

## Requirements

**mkvmerge** must be available in the container environment.

- **Official Docker image:** mkvmerge is included by default. No additional setup is required.
- **Manual installation:** Install the `mkvtoolnix` package for your distribution:
  ```bash
  # Debian/Ubuntu
  apt-get install mkvtoolnix

  # Alpine (Docker)
  apk add mkvtoolnix
  ```

If mkvmerge is not found, the Remove button in the Tracks panel will be greyed out and a tooltip will indicate the missing dependency.

## Undo

Stream removal is reversible during the trash retention period:

1. Go to **Settings → Automation → Subtitle Trash**.
2. Find the backed-up original file (it will have the original `.mkv` extension and be listed with the deletion timestamp from the remux operation).
3. Click **Restore**.

The original file is moved back to its media path, replacing the remuxed version.

After the retention period (default 7 days), the backup is automatically purged and the operation cannot be undone.

> [!TIP]
> Before removing embedded subtitle tracks from an entire series, test with a single episode first. Verify playback in Jellyfin or Emby to confirm the correct subtitle and audio tracks are selected after the remux.
