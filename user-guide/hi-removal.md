---
title: HI Removal (Hearing Impaired)
description: Strip hearing-impaired annotations — sound effects, speaker labels, and scene descriptions — from subtitle files.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# HI Removal (Hearing Impaired)

Remove sound descriptions, speaker labels, and other accessibility markers from subtitle files, leaving only spoken dialogue.

![HI Removal Settings](/img/screen-settings-subtitle-tools.png)

## Overview

Hearing-impaired (HI) subtitle tracks include annotations beyond spoken dialogue — sound effects in brackets, speaker labels before lines, music notation, and scene descriptions. These annotations exist to make content accessible to deaf and hard-of-hearing viewers and are included by default in many subtitle providers' tracks.

When watching with audio, these annotations are redundant and can break the reading flow, particularly when watching anime where fast dialogue leaves little time to process additional markers. HI Removal scans subtitle files and strips these annotations automatically, leaving only the spoken dialogue text.

## What Gets Removed

HI Removal targets the following patterns:

| Pattern | Example |
|---------|---------|
| Sound effects in square brackets | `[DOOR SLAMS]` |
| Sound effects in parentheses | `(gun fires)` |
| Speaker labels (ALL CAPS followed by colon) | `JOHN: I can't do this.` |
| Speaker labels (Title Case followed by colon) | `Narrator: Long ago...` |
| Music notation | `♪ Here we go again ♪` |
| Scene descriptions in brackets | `[upbeat music playing]` |
| Caption prefixes | `(narrator)` at the start of a line |
| Ambient sound labels | `[INDISTINCT CHATTER]` |

When a cue's entire text consists of annotations (nothing left after removal), the entire cue is deleted. When a cue has a mix of dialogue and inline annotations, only the annotation portion is removed and the remaining text is kept.

**Aggressive mode** also removes partial-line annotations in parentheses that appear mid-sentence or at the end of a line. For example:

- Normal mode: `I need to leave. (sighs)` → `I need to leave. (sighs)` (kept as-is, since the line is not purely HI)
- Aggressive mode: `I need to leave. (sighs)` → `I need to leave.`

## Configuration

Go to **Settings → Subtitle Tools → HI Removal**.

| Setting | Description | Default |
|---------|-------------|---------|
| Enable HI Removal | Master toggle for automatic HI removal | Off |
| Aggressive mode | Also removes partial-line parenthetical annotations mid-sentence | Off |
| Backup before HI removal | Save the original subtitle to Subtitle Trash before modifying | On |

It is strongly recommended to keep **Backup before HI removal** enabled, especially when enabling the feature for the first time. See the Backup section below.

## When HI Removal Runs

HI Removal is applied automatically at:

1. **After every subtitle download** — on the newly downloaded file, before it is assigned to the episode.
2. **After every LLM translation** — on the translated output file.

It can also be triggered manually for a specific episode:

1. Navigate to **Library**, open the series, and find the episode.
2. Click the **⋮ (three-dot)** action menu.
3. Select **Remove HI Markers**.

Manual triggering is useful when you enable HI Removal after already having subtitles downloaded, to clean up existing files without re-downloading.

## Backup

If the **Backup before HI removal** toggle is enabled (the default), Sublarr moves the original subtitle file to the [Subtitle Trash](subtitle-trash.md) before writing the cleaned version. This gives you a 7-day window (by default) to restore the original if the result is unsatisfactory.

To restore a subtitle that was modified by HI Removal:

1. Go to **Settings → Automation → Subtitle Trash**.
2. Find the file by series name and date.
3. Click **Restore**.

## Preview

Before enabling HI Removal globally, test it against a real subtitle file:

1. Go to **Settings → Subtitle Tools → Preview**.
2. Paste the contents of a subtitle file (ASS, SSA, or SRT) into the text field.
3. Select **HI Removal** from the preview mode selector.
4. Click **Preview**.
5. Lines that would be removed are highlighted in red. Partial removals (in Aggressive mode) are highlighted in orange. Lines that are kept unchanged are shown in green.

This lets you verify that the feature is not over-removing for a particular series before committing.

> [!TIP]
> Enable backup before first enabling HI removal. If the result is incorrect for a particular series — for example, if a show uses bracketed text as part of its visual style or on-screen text subtitles — you can restore the original from Subtitle Trash and disable HI removal for that series specifically via the series settings panel.
