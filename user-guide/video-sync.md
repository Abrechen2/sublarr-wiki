---
title: Video Sync (ffsubsync / alass)
description: Automatically correct subtitle timing by aligning the subtitle file to the video's audio track.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# Video Sync (ffsubsync / alass)

Automatically fix subtitle timing by aligning a subtitle file to the video's audio — no manual cue dragging required.

## Overview

Subtitle timing problems are common when a subtitle file was produced for a different release group's encode than the one you have. The dialogue is correct but every line appears a few hundred milliseconds too early or too late, or the drift increases over time.

Sublarr supports two automatic sync backends — **ffsubsync** and **alass** — that analyse the relationship between the subtitle file and the video file and produce a corrected subtitle with adjusted timestamps. Both tools are included in the official Sublarr Docker image and require no separate installation when using that image.

## When to Use

Use Video Sync when:

- The subtitle translation looks correct but dialogue is consistently offset from the spoken audio.
- You downloaded a subtitle from a provider and it was produced for a different encode (different source, different cut, different frame rate).
- The offset increases over the episode (linear drift), not just a fixed shift.

Video Sync is **not** the right tool when:

- The subtitle has missing lines, wrong translations, or structural errors — fix those first.
- The subtitle is already well-timed and you only need to adjust a few specific cues — use the [Waveform Editor](waveform-editor.md) instead.

## ffsubsync vs alass

| Feature | ffsubsync | alass |
|---------|-----------|-------|
| Alignment method | Correlates subtitle voice-activity windows against audio speech energy | Aligns subtitle timing using a reference subtitle (usually embedded in the video) |
| Speed | Slower (requires audio decoding) | Faster (no audio processing) |
| No speech required | Yes — works on audio alone | No — requires a reference subtitle stream in the video |
| Handles linear drift | Yes | Yes |
| Best for | Files with no embedded subs; general-purpose correction | Files that have an embedded subtitle stream to use as a reference; more accurate for complex drift |
| Typical accuracy | ±50–200 ms after sync | ±10–50 ms after sync |

**When to choose ffsubsync:** You have a raw video file with no embedded subtitle streams and a poorly-timed external subtitle.

**When to choose alass:** Your video file has at least one embedded subtitle track (e.g. a forced subs stream) that can serve as a reference. alass uses that reference's timing to align your external subtitle, which is typically more precise.

## Triggering a Sync

1. Navigate to **Library**, open the series, and find the episode.
2. Click the **⋮ (three-dot)** action menu on the episode row.
3. Select **Sync Timing**.
4. In the confirmation dialog, choose the backend (ffsubsync or alass) if you want to override the default. Otherwise, the configured default backend is used.
5. Click **Sync**.

Sublarr runs the sync job in the background. A spinner appears on the episode row while the job is running. Depending on file size and the selected backend, this typically takes 10–60 seconds. A notification is shown when the job completes.

## Sync Results

- The corrected subtitle is saved alongside the original with a `.synced.ass` suffix (e.g. `Episode.S01E01.synced.ass`).
- The original subtitle file is **not overwritten** — it is backed up to the [Subtitle Trash](subtitle-trash.md) before the synced version is promoted.
- Once the sync is complete, Sublarr automatically assigns the `.synced.ass` file as the active subtitle for the episode.
- If the sync result looks worse than the original (this can happen with very noisy audio or missing speech), you can restore the original from the Subtitle Trash view.

## Configuration

The default sync backend and additional options are configured in **Settings → Subtitle Tools → Sync**.

| Setting | Description | Default |
|---------|-------------|---------|
| Sync Backend | `ffsubsync` or `alass` | `ffsubsync` |
| Max offset (ffsubsync) | Maximum allowed correction in seconds — large values increase runtime | `60` |
| Framerate correction | Apply frame-rate conversion during sync if source/target FPS differ | Enabled |

## Requirements

- **ffsubsync** and **alass** are both included in the official Sublarr Docker image. No additional setup is needed.
- If you are running Sublarr outside Docker, both tools must be installed and available on `PATH`:
  - ffsubsync: `pip install ffsubsync`
  - alass: download the binary from [github.com/kaegi/alass](https://github.com/kaegi/alass) and place it on `PATH`

> [!TIP]
> If you find that neither backend produces good results, the subtitle file may have structural issues (e.g. all cues collapsed to time 00:00:00). Open the subtitle in the [Waveform Editor](waveform-editor.md) to inspect the cue distribution before attempting another sync.
