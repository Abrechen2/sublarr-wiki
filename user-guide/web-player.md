---
title: Web Player
description: In-browser video preview with subtitle overlay, available from the Library view.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# Web Player

Play episodes directly in the browser with a subtitle overlay — no external media player required.

![Web Player](/img/screen-web-player.png)

## Overview

The Web Player is an in-browser video preview modal introduced in **v0.29.0-beta**. It streams the video file directly from Sublarr's backend and renders the assigned subtitle track as an overlay inside the browser tab. It is intended for quick spot-checking of subtitle timing and readability, not as a full-featured media player replacement.

The player renders ASS/SSA subtitles using a client-side renderer, preserving styles, positioning, and colour information from the subtitle file. SRT subtitles are also supported and are displayed with a default style.

## Opening the Player

1. Navigate to **Library** and open a series.
2. Find the episode you want to preview.
3. Click the **⋮ (three-dot)** action menu on the episode row.
4. Select **Play**.

The player modal opens immediately. If no subtitle file is currently assigned to the episode, the video plays without a subtitle overlay. Assign a subtitle first from the episode's subtitle panel if you want to see it rendered.

## Controls & Shortcuts

The player supports both on-screen controls (play/pause button, seek bar, fullscreen toggle) and keyboard shortcuts for efficient review.

| Shortcut | Action |
|----------|--------|
| `Space` | Play / Pause |
| `←` | Seek backward 5 seconds |
| `→` | Seek forward 5 seconds |
| `[` | Subtitle delay −500 ms |
| `]` | Subtitle delay +500 ms |
| `F` | Toggle fullscreen |
| `Esc` | Close the player modal |

Subtitle delay adjustments made with `[` and `]` are session-only — they do not modify the subtitle file on disk. Use the [Waveform Editor](waveform-editor.md) or [Video Sync](video-sync.md) to make permanent timing corrections.

## Subtitle Overlay

ASS/SSA subtitle files are rendered client-side using a JavaScript ASS renderer. The following are preserved from the subtitle file:

- Font face and size (if the font is available in the browser; falls back to system sans-serif)
- Colour and border/shadow styles
- Positioning and alignment (e.g. top-positioned signs)
- Per-line override tags (`\an`, `\pos`, `\c`, etc.)

SRT subtitles are displayed centred at the bottom of the frame with a semi-transparent background.

## Limitations

- **No transcoding.** The video file is served as-is. Playback requires the browser to natively support the file's codec (H.264 in MKV/MP4 works in most browsers; H.265/HEVC may not). If the video does not play, use Jellyfin or Emby for transcoded playback.
- **No audio track switching.** Only the default audio track is available. Multi-audio MKV files cannot be switched inside the web player.
- **Single subtitle track.** Only the currently assigned sidecar subtitle is shown. Embedded subtitle streams in the container are not selectable from the player.
- **Seek performance.** Seeking in large files without a seekable byte-range server response may be slow on some deployments. Ensure Sublarr is not behind a reverse proxy that buffers the entire file before forwarding.
