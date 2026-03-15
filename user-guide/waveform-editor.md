---
title: Waveform Editor
description: Visual subtitle timing editor — drag cue handles on an audio waveform to fix timing.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# Waveform Editor

![Waveform Editor](/img/screen-waveform-editor.png)

Adjust subtitle cue timing visually by dragging handles directly on an audio waveform.

## Overview

The Waveform Editor renders the episode's audio as a scrollable waveform and overlays each subtitle cue as a coloured block. You can drag the start and end handles of any cue to shift or extend it, double-click the cue to edit its text inline, and play back any section to verify the result — all without leaving Sublarr.

The editor is suited for fine-grained corrections on individual cues. For broad timing shifts across an entire subtitle file (e.g. a consistent offset), use [Video Sync](video-sync.md) instead.

## Opening the Editor

1. The episode must already have a subtitle file assigned. If no subtitle is assigned, the editor cannot open.
2. Navigate to **Library**, open the series, and find the episode.
3. Click the **⋮ (three-dot)** action menu on the episode row.
4. Select **Edit Timing**.

The editor opens as a full-screen modal. Audio decoding runs in the browser — the initial waveform render may take a few seconds for long episodes.

## Timeline Controls

| Control | Action |
|---------|--------|
| Scroll wheel | Scroll the timeline horizontally |
| `Z` | Zoom in (expand time scale) |
| `X` | Zoom out (compress time scale) |
| Click on waveform (empty area) | Move the playback cursor to that position |
| `Space` | Play / Pause at the current cursor position |

The timeline shows timestamps along the top. Cues are rendered as semi-transparent coloured blocks that span from their start time to their end time. The cue text is shown inside the block when the zoom level is sufficient.

## Editing Cues

**Move a cue start or end:**
Hover over the left or right edge of a cue block until the cursor changes to a resize handle, then drag to the desired position. The timestamp updates live as you drag.

**Move an entire cue:**
Drag the body of the cue block (not the edges) to shift start and end together while preserving the cue's duration.

**Edit cue text:**
Double-click anywhere on the cue body. An inline text field appears over the cue. Edit the text and press `Enter` to confirm or `Escape` to cancel.

**Delete a cue:**
Right-click the cue block and select **Delete cue** from the context menu.

**Add a new cue:**
Right-click on an empty area of the waveform and select **Add cue here**. A new cue of default duration (2 seconds) is inserted at that position with placeholder text.

Changes are held in memory until you save. There is no auto-save.

## Save & Backup

When you press **Save** (or `Ctrl+S`), Sublarr:

1. Writes the current in-memory cue list to disk, overwriting the assigned subtitle file.
2. Before overwriting, creates a `.bak` copy of the previous file in the same directory (e.g. `Episode.S01E01.ass` → `Episode.S01E01.ass.bak`).

If you need to revert, you can find the `.bak` file via the [Subtitle Trash](subtitle-trash.md) view or by accessing the config directory directly.

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+S` | Save changes to disk (with backup) |
| `Space` | Play / Pause at cursor position |
| `Z` | Zoom in |
| `X` | Zoom out |
| `Escape` | Close the editor (prompts if unsaved changes exist) |

> [!TIP]
> Zoom in to individual cues before adjusting handles — at high zoom levels, 1 pixel of drag corresponds to just a few milliseconds, giving you precise control over cue timing.
