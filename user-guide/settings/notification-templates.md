---
title: Settings — Notification Templates
description: Customize the message format sent for each notification event using Jinja2-style templates
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

# Settings — Notification Templates

![Settings — Notification Templates](/img/screen-settings-notification-templates.png)

Sublarr sends notifications for key events (downloads, upgrades, errors). The message text for each event is fully customizable using Jinja2-style templates. This lets you tailor the format and content to suit your notification service — whether that is a Telegram message, a Pushover alert, or a Discord embed.

## Notification Settings

Configure where notifications are sent and which events trigger them.

| Setting | Default | Env Variable | Description |
|---------|---------|--------------|-------------|
| Notification URLs | *(empty)* | `SUBLARR_NOTIFICATION_URLS_JSON` | JSON array of [Apprise](https://github.com/caronc/apprise) URLs. Supports 100+ services including Telegram, Discord, Pushover, Slack, and email. Example: `["tgram://bottoken/chatid", "discord://webhook-id/token"]` |
| Notify on Download | `true` | `SUBLARR_NOTIFY_ON_DOWNLOAD` | Send a notification when a new subtitle is successfully downloaded. |
| Notify on Upgrade | `true` | `SUBLARR_NOTIFY_ON_UPGRADE` | Send a notification when an existing subtitle is replaced by a higher-quality one. |
| Notify on Batch Complete | `false` | `SUBLARR_NOTIFY_ON_BATCH_COMPLETE` | Send a summary notification when a batch search or translation job finishes. |
| Notify on Error | `true` | `SUBLARR_NOTIFY_ON_ERROR` | Send a notification when a backend error, provider failure, or translation failure occurs. |
| Notify Manual Actions | `false` | `SUBLARR_NOTIFY_MANUAL_ACTIONS` | Send notifications for manual actions (e.g. manual download, manual translation) in addition to automated ones. |

**Apprise URL examples:**

| Service | URL format |
|---------|-----------|
| Telegram | `tgram://bottoken/chatid` |
| Discord | `discord://webhook-id/token` |
| Pushover | `pover://user@token` |
| Slack | `slack://tokenA/tokenB/tokenC/channel` |
| Email (SMTP) | `mailto://user:pass@gmail.com` |
| Gotify | `gotify://hostname/token` |
| Ntfy.sh | `ntfy://topic` |

See the [Apprise documentation](https://github.com/caronc/apprise/wiki) for a full list of supported services.

---

> **Note:** The template format for each notification event is configured on this page. Notification destinations (URLs) and event toggles are set in **Settings → Notifications** via the table above.

---

## Available Templates

Each notification event has its own template with a specific set of variables available in the template context.

| Event | When it fires | Available variables |
|-------|--------------|---------------------|
| `on_download` | A new subtitle file is downloaded for an episode or movie | `title`, `episode`, `language`, `provider`, `score`, `format` |
| `on_upgrade` | An existing subtitle is replaced by a higher-quality one | `title`, `episode`, `language`, `old_score`, `new_score`, `provider` |
| `on_batch_complete` | A batch search or translation job finishes | `total`, `success`, `failed`, `duration_seconds` |
| `on_error` | A backend error, provider failure, or translation failure occurs | `service`, `message`, `timestamp` |

### Variable Reference

**`on_download` variables**

| Variable | Type | Example | Description |
|----------|------|---------|-------------|
| `title` | string | `Attack on Titan` | Series or movie title |
| `episode` | string | `S03E07` | Episode identifier (movies: empty string) |
| `language` | string | `German` | Full language name of the downloaded subtitle |
| `provider` | string | `OpenSubtitles` | Provider the subtitle was sourced from |
| `score` | integer | `87` | Quality score (0–100) assigned by Sublarr |
| `format` | string | `ASS` | Subtitle file format (`ASS`, `SRT`, `VTT`) |

**`on_upgrade` variables**

| Variable | Type | Example | Description |
|----------|------|---------|-------------|
| `title` | string | `Demon Slayer` | Series or movie title |
| `episode` | string | `S01E12` | Episode identifier |
| `language` | string | `English` | Language of the upgraded subtitle |
| `old_score` | integer | `58` | Score of the replaced subtitle |
| `new_score` | integer | `91` | Score of the new subtitle |
| `provider` | string | `Subscene` | Provider the better subtitle came from |

**`on_batch_complete` variables**

| Variable | Type | Example | Description |
|----------|------|---------|-------------|
| `total` | integer | `142` | Total number of items processed |
| `success` | integer | `138` | Items that completed successfully |
| `failed` | integer | `4` | Items that failed (no subtitle found or error) |
| `duration_seconds` | integer | `3720` | Wall-clock time the batch took to complete |

**`on_error` variables**

| Variable | Type | Example | Description |
|----------|------|---------|-------------|
| `service` | string | `Ollama` | Which subsystem raised the error |
| `message` | string | `Connection refused at localhost:11434` | Error detail |
| `timestamp` | string | `2026-03-15 14:32:01` | UTC timestamp of the error |

---

## Default Templates

Sublarr ships with sensible defaults for all events. Below is the built-in default for `on_download` as an illustrative example:

```jinja2
📥 Subtitle downloaded
{{ title }}{% if episode %} · {{ episode }}{% endif %}

Language: {{ language }}
Provider: {{ provider }}
Score: {{ score }}/100
Format: {{ format }}
```

The defaults for the other events follow the same pattern — a short heading line followed by the relevant data fields.

---

## Template Syntax

Sublarr templates use a Jinja2-compatible syntax:

| Syntax | Purpose | Example |
|--------|---------|---------|
| `{{ variable }}` | Insert a variable value | `{{ title }}` |
| `{% if condition %}...{% endif %}` | Conditional block — renders only when condition is truthy | `{% if episode %}· {{ episode }}{% endif %}` |
| `{% if x %}...{% else %}...{% endif %}` | Conditional with fallback | `{% if failed > 0 %}⚠️ {{ failed }} failed{% else %}✅ All done{% endif %}` |

**Example — a custom `on_batch_complete` template:**

```jinja2
Sublarr batch complete
{{ success }}/{{ total }} subtitles downloaded in {{ duration_seconds }}s
{% if failed > 0 %}
⚠️ {{ failed }} item(s) could not be resolved — check the Wanted list.
{% endif %}
```

**Example — a concise `on_error` template for Pushover:**

```jinja2
[Sublarr Error] {{ service }}
{{ message }}
({{ timestamp }})
```

---

## Resetting to Defaults

Each template has an individual **Reset** button that restores just that event's template to its built-in default. The **Reset All Templates** button at the bottom of the page restores all four templates at once.

Resetting does not affect notification URLs or enabled/disabled state — only the message text is reverted.
