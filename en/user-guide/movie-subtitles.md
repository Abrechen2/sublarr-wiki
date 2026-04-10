---
title: Movie Subtitle Management
description: Managing subtitle sidecar files for movies in Sublarr
published: true
date: 2026-04-10
---

# Movie Subtitle Management

As of v0.47.0-beta, movie detail pages display existing subtitle sidecar files alongside the full subtitle actions menu available for TV episodes.

## Viewing Movie Subtitles

Open any movie in the Library and navigate to its detail page. If subtitle sidecar files exist in the same folder as the movie file, they appear in the **Subtitles** section.

Each sidecar shows:
- Language and format (SRT / ASS)
- File size
- Actions menu

## Available Actions

| Action | Description |
|--------|-------------|
| **Remove HI** | Strip hearing-impaired annotations (CC, sound descriptions) |
| **Common Fixes** | Apply standard formatting corrections |
| **Shift Timing** | Apply a millisecond offset to all subtitle timestamps |
| **Download** | Download the sidecar file |

## Timing Offset Tool

The timing offset tool shifts all subtitle cues by a fixed number of milliseconds. Use positive values to delay subtitles, negative values to advance them.

This is useful when a subtitle file is slightly out of sync with a specific video encode.

## Notes

- Actions apply to the sidecar file on disk directly — no re-download needed
- The movie must be in your Library (connected via Radarr or standalone scan) for the detail page to appear
