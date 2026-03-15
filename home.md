---
title: Sublarr Wiki
description: Documentation for Sublarr — self-hosted subtitle manager for anime & media
published: true
date: 2026-03-14T21:50:38.377Z
tags: home
editor: markdown
dateCreated: 2026-03-14T18:04:40.770Z
---

<div class="version-hero">
  <span class="vh-icon mdi mdi-subtitles-outline"></span>
  <div class="vh-text">
    <div class="vh-label">Current Release</div>
    <div class="vh-version">Sublarr v0.29.0-beta</div>
    <div class="vh-desc">Self-hosted subtitle manager for anime & media libraries</div>
  </div>
  <span class="vh-badge">Web Player</span>
</div>

<div class="nav-section"><span class="ns-icon mdi mdi-rocket-launch-outline"></span><span class="ns-title">Getting Started</span></div>

<div class="nav-grid">
  <a class="nav-card" href="/getting-started/installation"><span class="nc-icon mdi mdi-docker"></span><div class="nc-body"><div class="nc-title">Installation</div><div class="nc-desc">Docker, Docker Compose, environment variables</div></div></a>
  <a class="nav-card" href="/getting-started/quick-start"><span class="nc-icon mdi mdi-play-circle-outline"></span><div class="nc-body"><div class="nc-title">Quick Start Guide</div><div class="nc-desc">Connect your *arr apps and find your first subtitles</div></div></a>
  <a class="nav-card" href="/getting-started/environment-variables"><span class="nc-icon mdi mdi-cog-outline"></span><div class="nc-body"><div class="nc-title">Environment Variables</div><div class="nc-desc">All SUBLARR_* configuration options</div></div></a>
  <a class="nav-card" href="/getting-started/upgrade-guide"><span class="nc-icon mdi mdi-update"></span><div class="nc-body"><div class="nc-title">Upgrade Guide</div><div class="nc-desc">Upgrading between versions, migration notes</div></div></a>
</div>

<div class="nav-section"><span class="ns-icon mdi mdi-book-open-outline"></span><span class="ns-title">User Guide</span></div>

<div class="nav-grid">
  <a class="nav-card" href="/user-guide/library"><span class="nc-icon mdi mdi-folder-play-outline"></span><div class="nc-body"><div class="nc-title">Library</div><div class="nc-desc">Browsing and managing your media library</div></div></a>
  <a class="nav-card" href="/user-guide/wanted"><span class="nc-icon mdi mdi-magnify"></span><div class="nc-body"><div class="nc-title">Wanted</div><div class="nc-desc">Automatic missing subtitle detection and search</div></div></a>
  <a class="nav-card" href="/user-guide/activity"><span class="nc-icon mdi mdi-history"></span><div class="nc-body"><div class="nc-title">Activity</div><div class="nc-desc">Translation jobs, download history</div></div></a>
  <a class="nav-card" href="/user-guide/web-player"><span class="nc-icon mdi mdi-play-circle-outline"></span><div class="nc-body"><div class="nc-title">Web Player</div><div class="nc-desc">In-browser video preview with subtitle overlay</div></div></a>
  <a class="nav-card" href="/user-guide/waveform-editor"><span class="nc-icon mdi mdi-waveform"></span><div class="nc-body"><div class="nc-title">Waveform Editor</div><div class="nc-desc">Visual subtitle timing editor</div></div></a>
  <a class="nav-card" href="/user-guide/translation-llm"><span class="nc-icon mdi mdi-brain"></span><div class="nc-body"><div class="nc-title">Translation & LLM</div><div class="nc-desc">Ollama, custom anime model, translation pipeline</div></div></a>
  <a class="nav-card" href="/user-guide/ai-glossary"><span class="nc-icon mdi mdi-book-alphabet"></span><div class="nc-body"><div class="nc-title">AI Glossary</div><div class="nc-desc">Per-series term overrides for translation</div></div></a>
  <a class="nav-card" href="/user-guide/video-sync"><span class="nc-icon mdi mdi-sync"></span><div class="nc-body"><div class="nc-title">Video Sync</div><div class="nc-desc">Auto-correct subtitle timing with ffsubsync / alass</div></div></a>
  <a class="nav-card" href="/user-guide/notifications"><span class="nc-icon mdi mdi-bell-outline"></span><div class="nc-body"><div class="nc-title">Notifications</div><div class="nc-desc">Telegram, Pushover, Discord via Apprise</div></div></a>
  <a class="nav-card" href="/user-guide/hi-removal"><span class="nc-icon mdi mdi-ear-hearing-off"></span><div class="nc-body"><div class="nc-title">HI Removal</div><div class="nc-desc">Strip hearing-impaired annotations</div></div></a>
  <a class="nav-card" href="/user-guide/credit-filtering"><span class="nc-icon mdi mdi-filter-remove-outline"></span><div class="nc-body"><div class="nc-title">Credit Filtering</div><div class="nc-desc">Remove fansub credits from subtitles</div></div></a>
  <a class="nav-card" href="/user-guide/stream-removal"><span class="nc-icon mdi mdi-track-changes"></span><div class="nc-body"><div class="nc-title">Stream Removal</div><div class="nc-desc">Remove embedded subtitle tracks via mkvmerge</div></div></a>
  <a class="nav-card" href="/user-guide/subtitle-trash"><span class="nc-icon mdi mdi-delete-restore"></span><div class="nc-body"><div class="nc-title">Subtitle Trash</div><div class="nc-desc">Soft-delete, restore, and auto-purge subtitles</div></div></a>
</div>

<div class="nav-section"><span class="ns-icon mdi mdi-tune-vertical"></span><span class="ns-title">Settings Reference</span></div>

<div class="nav-grid">
  <a class="nav-card" href="/user-guide/settings/general"><span class="nc-icon mdi mdi-cog-outline"></span><div class="nc-body"><div class="nc-title">General</div><div class="nc-desc">Host, port, authentication, backups</div></div></a>
  <a class="nav-card" href="/user-guide/settings/media-management"><span class="nc-icon mdi mdi-folder-cog-outline"></span><div class="nc-body"><div class="nc-title">Media Management</div><div class="nc-desc">Path mappings, sidecar conventions</div></div></a>
  <a class="nav-card" href="/user-guide/settings/profiles"><span class="nc-icon mdi mdi-translate"></span><div class="nc-body"><div class="nc-title">Language Profiles</div><div class="nc-desc">Per-series language targeting and priorities</div></div></a>
  <a class="nav-card" href="/user-guide/settings/providers"><span class="nc-icon mdi mdi-cloud-search-outline"></span><div class="nc-body"><div class="nc-title">Providers</div><div class="nc-desc">Subtitle provider list, priority, anti-captcha</div></div></a>
  <a class="nav-card" href="/user-guide/settings/scoring"><span class="nc-icon mdi mdi-chart-bar"></span><div class="nc-body"><div class="nc-title">Scoring</div><div class="nc-desc">Score weights, thresholds, cutoffs</div></div></a>
  <a class="nav-card" href="/user-guide/settings/translation"><span class="nc-icon mdi mdi-brain"></span><div class="nc-body"><div class="nc-title">Translation</div><div class="nc-desc">LLM provider, model, language pair</div></div></a>
  <a class="nav-card" href="/user-guide/settings/translation-backends"><span class="nc-icon mdi mdi-server-outline"></span><div class="nc-body"><div class="nc-title">Translation Backends</div><div class="nc-desc">Ollama, OpenAI, Anthropic, Custom HTTP</div></div></a>
  <a class="nav-card" href="/user-guide/settings/prompt-templates"><span class="nc-icon mdi mdi-text-box-edit-outline"></span><div class="nc-body"><div class="nc-title">Prompt Templates</div><div class="nc-desc">Custom LLM translation prompts</div></div></a>
  <a class="nav-card" href="/user-guide/settings/whisper"><span class="nc-icon mdi mdi-microphone-outline"></span><div class="nc-body"><div class="nc-title">Whisper</div><div class="nc-desc">Audio transcription settings</div></div></a>
  <a class="nav-card" href="/user-guide/settings/subtitle-tools"><span class="nc-icon mdi mdi-tools"></span><div class="nc-body"><div class="nc-title">Subtitle Tools</div><div class="nc-desc">HI removal, credit filtering, sync, common fixes</div></div></a>
  <a class="nav-card" href="/user-guide/settings/automation"><span class="nc-icon mdi mdi-timer-outline"></span><div class="nc-body"><div class="nc-title">Automation</div><div class="nc-desc">Scan schedules, upgrade rules, auto-cleanup</div></div></a>
  <a class="nav-card" href="/user-guide/settings/sonarr"><span class="nc-icon mdi mdi-television-play"></span><div class="nc-body"><div class="nc-title">Sonarr</div><div class="nc-desc">TV & anime library sync, webhooks</div></div></a>
  <a class="nav-card" href="/user-guide/settings/radarr"><span class="nc-icon mdi mdi-movie-open-outline"></span><div class="nc-body"><div class="nc-title">Radarr</div><div class="nc-desc">Movie library sync, webhooks</div></div></a>
  <a class="nav-card" href="/user-guide/settings/media-sources"><span class="nc-icon mdi mdi-folder-search-outline"></span><div class="nc-body"><div class="nc-title">Media Sources</div><div class="nc-desc">Standalone mode, watched folders, TMDB/TVDB</div></div></a>
  <a class="nav-card" href="/user-guide/settings/media-server"><span class="nc-icon mdi mdi-plex"></span><div class="nc-body"><div class="nc-title">Media Server</div><div class="nc-desc">Jellyfin / Emby subtitle refresh</div></div></a>
  <a class="nav-card" href="/user-guide/settings/integrations"><span class="nc-icon mdi mdi-connection"></span><div class="nc-body"><div class="nc-title">Integrations</div><div class="nc-desc">Bazarr migration, health diagnostics</div></div></a>
  <a class="nav-card" href="/user-guide/settings/notification-templates"><span class="nc-icon mdi mdi-bell-cog-outline"></span><div class="nc-body"><div class="nc-title">Notification Templates</div><div class="nc-desc">Per-event message templates</div></div></a>
  <a class="nav-card" href="/user-guide/settings/events-hooks"><span class="nc-icon mdi mdi-webhook"></span><div class="nc-body"><div class="nc-title">Events & Hooks</div><div class="nc-desc">Outgoing webhooks for automation</div></div></a>
  <a class="nav-card" href="/user-guide/settings/backup"><span class="nc-icon mdi mdi-backup-restore"></span><div class="nc-body"><div class="nc-title">Backup</div><div class="nc-desc">Schedule, retention, restore procedure</div></div></a>
  <a class="nav-card" href="/user-guide/settings/cleanup"><span class="nc-icon mdi mdi-broom"></span><div class="nc-body"><div class="nc-title">Cleanup</div><div class="nc-desc">Orphan files, database maintenance</div></div></a>
  <a class="nav-card" href="/user-guide/settings/security"><span class="nc-icon mdi mdi-shield-lock-outline"></span><div class="nc-body"><div class="nc-title">Security</div><div class="nc-desc">API key, sessions, CORS</div></div></a>
</div>

<div class="nav-section"><span class="ns-icon mdi mdi-wrench-outline"></span><span class="ns-title">Troubleshooting</span></div>

<div class="nav-grid">
  <a class="nav-card" href="/troubleshooting/general"><span class="nc-icon mdi mdi-bug-outline"></span><div class="nc-body"><div class="nc-title">General Troubleshooting</div><div class="nc-desc">Common issues and solutions</div></div></a>
  <a class="nav-card" href="/troubleshooting/reverse-proxy"><span class="nc-icon mdi mdi-server-network"></span><div class="nc-body"><div class="nc-title">Reverse Proxy Guide</div><div class="nc-desc">nginx, Caddy, NPM setup</div></div></a>
  <a class="nav-card" href="/troubleshooting/performance-tuning"><span class="nc-icon mdi mdi-speedometer"></span><div class="nc-body"><div class="nc-title">Performance Tuning</div><div class="nc-desc">Large libraries, translation throughput</div></div></a>
</div>

<div class="nav-section"><span class="ns-icon mdi mdi-code-braces"></span><span class="ns-title">Development</span></div>

<div class="nav-grid">
  <a class="nav-card" href="/development/architecture"><span class="nc-icon mdi mdi-sitemap-outline"></span><div class="nc-body"><div class="nc-title">Architecture</div><div class="nc-desc">System design, component overview</div></div></a>
  <a class="nav-card" href="/development/api-reference"><span class="nc-icon mdi mdi-api"></span><div class="nc-body"><div class="nc-title">API Reference</div><div class="nc-desc">REST API endpoints</div></div></a>
  <a class="nav-card" href="/development/plugin-development"><span class="nc-icon mdi mdi-puzzle-outline"></span><div class="nc-body"><div class="nc-title">Plugin Development</div><div class="nc-desc">Writing custom provider/hook plugins</div></div></a>
  <a class="nav-card" href="/development/contributing"><span class="nc-icon mdi mdi-source-branch"></span><div class="nc-body"><div class="nc-title">Contributing</div><div class="nc-desc">Development workflow, PR guidelines</div></div></a>
</div>

<div class="nav-section"><span class="ns-icon mdi mdi-link-variant"></span><span class="ns-title">Links</span></div>

<div class="link-row">
  <a class="link-pill" href="https://sublarr.de"><span class="lp-icon mdi mdi-web"></span>sublarr.de</a>
  <a class="link-pill" href="https://github.com/Abrechen2/sublarr"><span class="lp-icon mdi mdi-github"></span>GitHub</a>
  <a class="link-pill" href="https://huggingface.co/Sublarr"><span class="lp-icon mdi mdi-brain"></span>HuggingFace</a>
  <a class="link-pill" href="https://www.paypal.com/donate?hosted_button_id=GLXYTD3FV9Y78"><span class="lp-icon mdi mdi-heart-outline"></span>Donate</a>
</div>