---
title: Installation
description: How to install Sublarr with Docker or Docker Compose
published: true
date: 2026-03-14
---

# Installation

## Quick Start

### Prerequisites

- **Docker** or Docker Compose (recommended)
- **Media library** accessible on the filesystem
- **(Optional)** Sonarr and/or Radarr for library management
- **(Optional)** Ollama or another translation backend for LLM-based subtitle translation

### Docker Compose (Recommended)

Create a `docker-compose.yml`:

```yaml
services:
  sublarr:
    image: ghcr.io/abrechen2/sublarr:0.19.2-beta
    container_name: sublarr
    ports:
      - "5765:5765"
    volumes:
      - ./config:/config        # Application config and database
      - /path/to/media:/media:rw  # Your media library
    environment:
      - PUID=1000               # User ID for file permissions
      - PGID=1000               # Group ID for file permissions
    restart: unless-stopped
```

Start it:

```bash
docker compose up -d
```

Then open `http://localhost:5765` in your browser. The onboarding wizard will guide you through initial setup.

### Unraid

1. Go to the **Apps** tab in Unraid
2. Search for "Sublarr" in Community Applications
3. Click **Install**
4. Configure the template:
   - **Config Path:** `/mnt/user/appdata/sublarr`
   - **Media Path:** Your media share (e.g., `/mnt/user/media`)
   - **Sonarr/Radarr URLs and API Keys** (optional)
   - **Ollama URL** (if using translation)
5. Click **Apply**

### Environment Variables

All settings use the `SUBLARR_` prefix. Key variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `SUBLARR_PORT` | `5765` | Web UI port |
| `SUBLARR_API_KEY` | *(empty)* | Optional API key for authentication |
| `SUBLARR_MEDIA_PATH` | `/media` | Root media library path |
| `SUBLARR_SONARR_URL` | *(empty)* | Sonarr instance URL |
| `SUBLARR_SONARR_API_KEY` | *(empty)* | Sonarr API key |
| `SUBLARR_RADARR_URL` | *(empty)* | Radarr instance URL |
| `SUBLARR_RADARR_API_KEY` | *(empty)* | Radarr API key |
| `SUBLARR_OLLAMA_URL` | `http://localhost:11434` | Ollama server URL |
| `SUBLARR_OLLAMA_MODEL` | `qwen2.5:14b-instruct` | Ollama model for translation |
| `SUBLARR_SOURCE_LANGUAGE` | `en` | Source subtitle language |
| `SUBLARR_TARGET_LANGUAGE` | `de` | Target translation language |

Most configuration is managed through the web UI after initial setup. Environment variables provide defaults that can be overridden in Settings.

## Setup Scenarios

### Scenario 1: Sonarr + Radarr (Recommended)

The most common setup -- Sublarr monitors your Sonarr/Radarr libraries and handles subtitles automatically.

**Step 1: Configure Sonarr**

1. In Sublarr Settings, go to the **Sonarr** tab
2. Enter your Sonarr URL (e.g., `http://192.168.1.100:8989`)
3. Enter your Sonarr API key (found in Sonarr > Settings > General)
4. Click **Test** to verify connection
5. **Path Mapping:** If Sonarr sees media at a different path than Sublarr (common in Docker), configure path mappings:
   - Remote Path: `/tv` (path in Sonarr)
   - Local Path: `/media/tv` (path in Sublarr)

**Step 2: Configure Radarr** (optional, for movies)

1. Same process as Sonarr in the **Radarr** tab
2. URL, API key, and path mappings

**Step 3: Set Up Webhooks**

Webhooks enable real-time subtitle processing when new media is downloaded.

In **Sonarr:**
1. Go to Settings > Connect
2. Add a new Webhook
3. URL: `http://sublarr:5765/api/v1/webhook/sonarr`
4. Trigger on: Import, Upgrade

In **Radarr:**
1. Go to Settings > Connect
2. Add a new Webhook
3. URL: `http://sublarr:5765/api/v1/webhook/radarr`
4. Trigger on: Import, Upgrade

**Step 4: Create Language Profiles**

1. Go to Settings > Advanced > Language Profiles
2. Create a profile (e.g., "Anime German"):
   - Source Language: English (or Japanese for anime)
   - Target Languages: German
   - Translation Backend: Ollama (or your preferred backend)
   - Forced Preference: Separate (to handle signs/songs)
3. Assign profiles to series in the Library view

**Step 5: Enable Providers**

1. Go to Settings > Providers
2. Enable desired providers and enter API keys where required:
   - AnimeTosho: No API key needed (great for anime)
   - OpenSubtitles: API key required (best general coverage)
   - SubDL: API key required (Subscene successor)
   - Jimaku: API key required (anime-focused)
3. Adjust provider priorities (higher priority = searched first)

### Scenario 2: Standalone Mode (No *arr Apps)

Use Sublarr without Sonarr or Radarr by pointing it at media folders directly.

**Step 1: Configure Library Sources**

1. Go to Settings > Advanced > Library Sources
2. Click **Add Watched Folder**
3. Enter the folder path (must be accessible inside the container, e.g., `/media/anime`)
4. Select content type: TV Shows, Movies, or Mixed
5. Enable auto-scan for automatic file detection

**Step 2: Configure Metadata Providers**

Standalone mode needs metadata providers to identify your media:

1. **AniList** (always available, no API key): Best for anime identification
2. **TMDB** (API key required): Best for general movies and TV shows
   - Get a free API key at https://www.themoviedb.org/settings/api
   - Enter in Settings > Library Sources > TMDB API Key
3. **TVDB** (API key required): Alternative TV show metadata
   - Get an API key at https://thetvdb.com/dashboard/account/apikey

**Step 3: Initial Scan**

1. After configuring watched folders, click **Scan Now** on the Library Sources page
2. Sublarr will:
   - Detect all media files in your folders
   - Parse filenames using `guessit` (anime-aware)
   - Look up metadata from configured providers
   - Group files into series/movies
   - Add items missing subtitles to the Wanted list

**Step 4: Ongoing Monitoring**

The `MediaFileWatcher` continuously monitors your folders for new files. When detected:
1. File stability check (waits for download completion)
2. Filename parsing and metadata lookup
3. Automatic addition to Wanted list
4. Subtitle search and download (if auto-search enabled)

### Scenario 3: Mixed Mode

Run both Sonarr/Radarr integration and standalone mode simultaneously.

1. Configure Sonarr/Radarr as in Scenario 1
2. Add watched folders for media not managed by *arr apps
3. Both sources feed into the same Wanted pipeline
4. Items are tagged with their source (sonarr/radarr/standalone) for clarity
