---
title: Settings — Media Sources
description: Standalone mode, watched folders, and metadata API keys for library scanning without Sonarr/Radarr
published: true
date: 2026-03-15
tags: settings
editor: markdown
dateCreated: 2026-03-15
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — Media Sources

![Settings — Media Sources](/img/screen-settings-media-sources.png)

Media Sources configures how Sublarr discovers your media library. By default, Sublarr uses Sonarr and Radarr as its library sources. Standalone mode allows direct filesystem scanning without any \*arr dependency.

## Standalone Mode

| Setting | Default | Description |
|---------|---------|-------------|
| **Enable Standalone Mode** | `off` | Scan the media directory directly instead of using Sonarr/Radarr. Requires `SUBLARR_MEDIA_PATH` to be set. |

When standalone mode is enabled, Sublarr scans the configured media path for video files and populates the library from the filesystem. No Sonarr or Radarr connection is needed. Metadata (series title, episode number, year) is inferred from filenames and resolved via TMDB/TVDB.

> [!NOTE]
> Standalone mode and Sonarr/Radarr mode can be used simultaneously. Series found via standalone scanning appear in the library alongside \*arr-managed content.

## Metadata API Keys

| Setting | Description |
|---------|-------------|
| **TMDB API Key (Bearer Token)** | Bearer token from [themoviedb.org](https://www.themoviedb.org/settings/api). Used for movie and series metadata lookup. Required for standalone mode. |
| **TVDB API Key (Optional)** | API key from [thetvdb.com](https://thetvdb.com/api-information). Optional — TMDB is sufficient for most use cases. |
| **TVDB PIN (Optional)** | Subscriber PIN for TVDB paid plan. Only required for TVDB subscriber-plan features. |

> [!TIP]
> TMDB offers a free API tier with no rate-limit issues for typical homelab use. Register at thetvdb.com → API access to get your bearer token.

## Watched Folders

Watched folders enable real-time scanning when new files appear in a directory. Click **Add Folder** to configure one or more paths.

Each watched folder entry has:
- **Path** — absolute path inside the container (e.g. `/media/anime`)
- **Recursive** — whether to scan subdirectories
- **Auto-search** — automatically trigger a provider search for newly detected episodes

Click **Scan All** to immediately scan all configured watched folders without waiting for the next scheduled scan.

> [!NOTE]
> The Watched Folders watcher shows a **Stopped** status badge when the filesystem watcher service is not running. It starts automatically when at least one folder is configured and Sublarr is restarted.
