---
title: Library
description: Browsing and managing your media library in Sublarr
published: true
date: 2026-03-14
---

# Library

The Library view shows all series and movies that Sublarr is aware of, sourced from your connected Jellyfin, Emby, or *arr integrations.

## Series List

Each row shows:
- **Title** — series name with year
- **Subtitle status** — how many episodes have subtitles vs. total
- **Language** — active language profile
- **Provider last used** — which provider found the last subtitle

Click a series to open the **Series Detail** view showing per-episode subtitle status.

## Series Detail

- Lists all seasons and episodes
- Shows subtitle file path, score, provider, and language for each episode
- **Search** button triggers a manual provider search for that episode
- **Translate** button sends the subtitle through the LLM translation pipeline
- **Delete** removes the subtitle file (moves to Trash)

## Filters

Use the filter bar to narrow by:
- Subtitle status: `all`, `subtitled`, `missing`, `wanted`
- Language profile
- Provider

## Bulk Actions

Select multiple series with the checkbox column, then:
- **Batch Search** — searches all selected series for missing subtitles
- **Batch Translate** — queues all selected for translation
