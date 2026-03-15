---
title: Credit Filtering
description: Automatically strip fansub credit lines and translator notes from the start and end of subtitle files.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# Credit Filtering

Automatically remove fansub credit lines, translator notes, and encoding credits from downloaded subtitle files.

## Overview

Subtitle files sourced from fansub groups and some providers frequently include lines crediting the translation, timing, quality-control, and encoding team. These lines appear most commonly during the opening and ending sequences of anime episodes — the first and last few minutes — where they ride over the OP/ED visuals. They are not part of the actual dialogue and clutter the viewing experience when using a clean subtitle track.

Credit Filtering scans subtitle files within a configurable time window at the start and end of each episode and removes any line that matches known credit patterns. It runs automatically after every subtitle download and after every translation, so you do not need to trigger it manually.

## What Gets Filtered

Credit filtering checks lines in two time windows:

- **Start window** (default: first 120 seconds) — covers the opening sequence
- **End window** (default: last 60 seconds) — covers the ending sequence

Within these windows, any subtitle cue whose text matches a credit pattern is removed. The built-in patterns target common fansub credit language:

| Pattern (case-insensitive) | Example match |
|---------------------------|---------------|
| `translated by` | `Translated by Fansub-Group` |
| `translation by` | `Translation by somebody` |
| `timed by` | `Timed by username` |
| `timing by` | `Timing: username` |
| `edited by` | `Edited by username` |
| `encoding by` | `Encoding: username` |
| `encoded by` | `Encoded by StudioX` |
| `QC by` | `QC by username` |
| `quality check` | `Quality check: username` |
| `script by` | `Script by Fansub-Group` |
| `fansubbed by` | `Fansubbed by Fansub-Group` |
| `kfx by` | `KFX by AnimeSubs` |
| `karaoke by` | `Karaoke by username` |
| Lines consisting solely of a group name following `[` / `(` | `[Fansub-Group]` |

Custom regex patterns can be added in Settings to handle group-specific credit formats not covered by the built-in list.

## Configuration

Go to **Settings → Subtitle Tools → Credit Filtering**.

| Setting | Description | Default |
|---------|-------------|---------|
| Enable Credit Filtering | Master toggle for the feature | Off |
| Start window (seconds) | How many seconds from the start of the episode to check | `120` |
| End window (seconds) | How many seconds from the end of the episode to check | `60` |
| Custom patterns | Additional regex patterns, one per line | (empty) |
| Backup before filtering | Save the original to Subtitle Trash before modifying | On |

**Adding custom patterns:**

```
# Custom credit patterns (one regex per line, case-insensitive)
by the \w+ team
visit us at .*\.com
\[.*fansub.*\]
```

Lines starting with `#` are treated as comments and ignored.

## When Filtering Runs

Credit filtering is applied automatically at two points:

1. **After every subtitle download** — immediately after a subtitle file is written to disk.
2. **After every LLM translation** — on the translated output file, before it replaces the source subtitle.

It can also be triggered manually from the episode action menu:

1. Navigate to **Library**, open the series, and find the episode.
2. Click the **⋮ (three-dot)** action menu.
3. Select **Remove Credits**.

## Preview

Before enabling credit filtering globally, use the preview tool to verify which lines would be removed from a real subtitle file:

1. Go to **Settings → Subtitle Tools → Preview**.
2. Paste the contents of a subtitle file into the text field.
3. Click **Preview Credit Filtering**.
4. Lines that would be removed are highlighted in red. Lines that would be kept are shown normally.

This lets you validate your custom patterns and window sizes against real content before they affect any stored subtitles.

## Impact on Timing

Surrounding cues are **not re-timed** after credit lines are removed. The gaps left by removed credit cues are natural silences during OP/ED music sections and do not require any timing adjustment. The rest of the subtitle file's timing is unchanged.

> [!TIP]
> If you find that credit filtering is removing lines it should not — for example, a character whose dialogue happens to contain "translated by" — add a **negative lookahead** in a custom pattern to exclude it, or reduce the start/end window size so the filtering only covers the actual OP/ED duration for the series.
