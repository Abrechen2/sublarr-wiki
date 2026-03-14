---
title: Wanted
description: Automatic missing subtitle detection and search
published: true
date: 2026-03-14
---

# Wanted

The Wanted list tracks all episodes and movies in your library that are missing subtitles matching your Language Profile. Sublarr automatically populates this list from your connected Jellyfin/Emby library.

### Wanted System

The Wanted system tracks media items missing subtitles.

**How it works:**
1. **Scan:** Periodically checks Sonarr/Radarr libraries (or standalone folders) for items without target-language subtitles
2. **Incremental scan:** Only checks items modified since the last scan (full scan every 6th cycle)
3. **Search:** Queries all enabled providers for matching subtitles
4. **Download:** Downloads the highest-scored result
5. **Translate:** Sends through the translation pipeline if needed

**Manual actions:**
- Click **Search** on any Wanted item to trigger an immediate search
- Click **Process** to download and translate the best available result
- Use **Batch Search** to search multiple items at once
