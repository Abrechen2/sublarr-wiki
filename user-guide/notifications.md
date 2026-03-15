---
title: Notifications
description: Send alerts for downloads, upgrades, and errors to Telegram, Pushover, Discord, and 80+ other services via Apprise.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# Notifications

Get notified when subtitles are downloaded, upgraded, batch jobs complete, or errors occur — using any service supported by Apprise.

## Overview

Sublarr uses [Apprise](https://github.com/caronc/apprise) as its notification backend. Apprise is a unified notification library that supports over 80 notification services via simple URL-based configuration. You configure notifications by pasting one or more Apprise URLs into Sublarr's settings — no API wrappers or service-specific plugins to install.

Notification settings are found at **Settings → General → Notification Settings**.

## Supported Services

Any service that Apprise supports works with Sublarr. The most common ones used in homelab deployments:

| Service | Apprise URL Format |
|---------|-------------------|
| Telegram | `tgram://bottoken/chatid` |
| Pushover | `pover://userkey@apptoken` |
| Discord | `discord://webhookid/token` |
| Slack | `slack://tokenA/tokenB/tokenC/#channel` |
| Email (Gmail) | `mailto://user:pass@gmail.com` |
| Email (SMTP) | `mailtos://user:pass@smtp.example.com:587` |
| Gotify | `gotify://hostname/apptoken` |
| Ntfy.sh | `ntfys://topic` or `ntfy://hostname/topic` |
| Matrix | `matrix://user:pass@hostname/#room` |

For a full list of supported services and their URL formats, refer to the [Apprise wiki](https://github.com/caronc/apprise/wiki).

## Configuring Notifications

1. Go to **Settings → General**.
2. Scroll to **Notification Settings**.
3. In the **Apprise URLs** field, enter one or more Apprise notification URLs — one per line.
4. Click **Save**.

Multiple URLs are supported. When a notification event fires, Sublarr sends the message to all configured URLs simultaneously.

**Example — Telegram + Pushover:**
```
tgram://7812345678:AAHxxxxxxxxxxxxxx/123456789
pover://uabc123xyz@aapp456xyz
```

## Notification Events

Each event type can be individually enabled or disabled.

| Event | Default | Description |
|-------|---------|-------------|
| `on_download` | Enabled | A subtitle was downloaded for an episode |
| `on_upgrade` | Enabled | A subtitle was replaced with a higher-scored version |
| `on_batch_complete` | Enabled | A batch search or translation job finished |
| `on_error` | Enabled | An error occurred during a download, sync, or translation job |
| `on_translation_complete` | Enabled | LLM translation finished for an episode |

To toggle individual events, go to **Settings → General → Notification Events** and use the enable/disable toggles next to each event type.

## Testing

After configuring your Apprise URLs, click the **Test Notification** button in **Settings → General**. Sublarr sends a test message to all configured URLs immediately. Check that the message arrives in all configured destinations before relying on notifications for production use.

If the test message does not arrive:

1. Verify the URL format against the [Apprise wiki](https://github.com/caronc/apprise/wiki) for your service.
2. Check the Sublarr logs (Settings → Logs or `docker logs sublarr`) for Apprise error output — the error message usually identifies the URL or authentication problem.
3. Confirm that the Sublarr container has outbound internet access if using a cloud notification service.

## Notification Templates

For advanced use, you can customise the message body for each event type.

Go to **Settings → Notification Templates**. Each event has a template field that supports the following variables:

| Variable | Description |
|----------|-------------|
| `{series_title}` | Series name |
| `{episode}` | Episode identifier (e.g. S01E04) |
| `{episode_title}` | Episode title |
| `{subtitle_provider}` | Provider the subtitle was downloaded from |
| `{subtitle_score}` | Provider match score |
| `{language}` | Subtitle language |
| `{error_message}` | Error description (only in `on_error` template) |

Default templates are used if a template field is left blank. Custom templates are applied per event type, so you can have a compact one-liner for `on_download` and a more detailed format for `on_error`.

> [!TIP]
> For Telegram notifications, keep templates short — Telegram truncates messages beyond ~4096 characters. Use `{series_title}` and `{episode}` as the minimum to identify what triggered the event.
