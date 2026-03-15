---
title: Settings — Events & Hooks
description: Webhooks and event hooks — trigger external HTTP endpoints when Sublarr events occur
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

# Settings — Events # Settings — Events & Hooks Hooks

![Settings — Events # Settings — Events & Hooks Hooks](/img/screen-settings-events-hooks.png)

Event hooks let Sublarr call external HTTP endpoints when specific events occur. This is the integration point for workflow automation tools like **n8n**, **Home Assistant**, **Zapier**, or any custom HTTP receiver. Unlike the Apprise-based notification system (which handles human-readable alerts), hooks send structured JSON payloads intended for machine consumption.

---

## Configured Hooks

The hooks overview table lists all currently configured hooks.

| Column | Description |
|--------|-------------|
| **Name** | Friendly label for the hook (e.g. `n8n translation pipeline`) |
| **URL** | The endpoint Sublarr will POST to |
| **Events** | Which events trigger this hook (comma-separated) |
| **Status** | `active` (green) or `inactive` (grey) — inactive hooks are saved but not called |

Click any row to edit the hook, or use the **Add Hook** button to create a new one.

---

## Adding a Hook

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| **Name** | text | Yes | Human-readable label — only used in this UI |
| **URL** | text | Yes | Full URL including scheme: must start with `http://` or `https://` |
| **Events** | multi-select | Yes | One or more events that trigger this hook (see list below) |
| **Method** | select | — | Always `POST` — Sublarr only sends POST requests |
| **Headers** | key-value pairs | No | Optional HTTP headers to include (e.g. `Authorization: Bearer <token>`) |
| **Enabled** | toggle | — | When off, the hook is saved but will not fire |

### Available Events

| Event | Fires when... |
|-------|--------------|
| `on_download` | A subtitle is successfully downloaded for any item |
| `on_upgrade` | An existing subtitle is replaced with a higher-scored one |
| `on_translation_complete` | LLM translation of a subtitle file finishes (success or failure) |
| `on_error` | Any backend error occurs (provider failure, LLM timeout, etc.) |
| `on_scan_complete` | A library scan finishes (scheduled or manual) |

---

## Payload Format

Sublarr sends a JSON `POST` body for every hook invocation. The envelope is consistent across all events:

```json
{
  "event": "on_download",
  "timestamp": "2026-03-15T14:32:01Z",
  "data": { ... }
}
```

The `data` object contains event-specific fields. Example payload for `on_download`:

```json
{
  "event": "on_download",
  "timestamp": "2026-03-15T14:32:01Z",
  "data": {
    "title": "Attack on Titan",
    "episode": "S03E07",
    "series_id": 42,
    "episode_id": 381,
    "language": "de",
    "language_name": "German",
    "provider": "OpenSubtitles",
    "score": 87,
    "format": "ASS",
    "subtitle_path": "/media/anime/Attack on Titan/Season 3/Attack on Titan - S03E07.de.ass"
  }
}
```

Example payload for `on_scan_complete`:

```json
{
  "event": "on_scan_complete",
  "timestamp": "2026-03-15T15:00:03Z",
  "data": {
    "scanned": 1204,
    "new_items": 12,
    "removed_items": 0,
    "duration_seconds": 8
  }
}
```

Sublarr sets the `Content-Type: application/json` header on all hook requests. Any additional headers you configure in the hook definition are appended on top.

---

## Retry Policy

When a hook endpoint returns a non-2xx status code or the connection times out, Sublarr retries the delivery.

| Setting | Default | Description |
|---------|---------|-------------|
| **Retries** | `3` | Maximum number of delivery attempts after the first failure |
| **Retry delay** | `10` seconds | Wait between retry attempts (fixed interval, not exponential) |
| **Timeout** | `30` seconds | Per-request connection + read timeout |

If all retries are exhausted, Sublarr logs a warning and continues — hook delivery failures do not affect subtitle processing.

---

## Testing

Each hook entry has a **Test** button. Clicking it sends a synthetic `on_download` payload to the configured URL using the current headers and timeout settings. The UI displays the HTTP status code and response body returned by the endpoint, making it easy to verify connectivity and authentication before relying on the hook in production.

The test payload uses placeholder data (series title `Test Series`, episode `S01E01`, score `75`) and is clearly marked with `"test": true` in the `data` object so your receiver can distinguish it from real events.
