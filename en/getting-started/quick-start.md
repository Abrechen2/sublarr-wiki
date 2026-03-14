---
title: Quick Start Guide
description: Connect Sublarr to your *arr stack and find your first subtitles
published: true
date: 2026-03-14
---

# Quick Start Guide

## 1. Open Sublarr

After running `docker compose up -d`, open [http://localhost:5765](http://localhost:5765) in your browser.

## 2. Configure a Provider

Go to **Settings → Providers** and enable at least one subtitle provider:

- **AnimeTosho** — best for anime (no API key required)
- **OpenSubtitles** — large library, requires free account
- **Jimaku** — Japanese anime focus

Click **Test** to verify the provider is reachable.

## 3. Set Up Language Profile

Go to **Settings → Profiles → Language Profiles** and create a profile:
- **Source language:** English (en)
- **Target language:** German (de) or your preferred language
- **Minimum score:** 60 (recommended)

## 4. Connect a Media Server

Go to **Settings → Integrations** and add Jellyfin or Emby:
- Enter your server URL and API key
- Click **Test Connection**

Sublarr will scan your library and populate the Wanted list with missing subtitles.

## 5. Connect Sonarr / Radarr (optional)

Go to **Settings → Integrations → Sonarr** (or Radarr):
- Enter URL: `http://sonarr:8989`
- Enter API key (found in Sonarr → Settings → General)
- Enable webhooks: in Sonarr, add a webhook pointing to `http://sublarr:5765/api/v1/webhook/sonarr`

New downloads will now trigger automatic subtitle search.

## 6. Search for Subtitles

Go to **Wanted** — click **Search All** to start finding subtitles for everything in your library.

> **Tip:** The first scan can take a while for large libraries. Watch progress in **Activity → Tasks**.
