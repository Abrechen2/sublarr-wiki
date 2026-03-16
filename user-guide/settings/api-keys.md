---
title: Settings — API Keys
description: Centralized API key registry — manage, test, rotate, and export/import all service credentials
published: true
date: 2026-03-16
tags: settings
editor: markdown
dateCreated: 2026-03-16
---

<div class="settings-tabs">
<a class="settings-tab " href="/user-guide/settings/general"><span class="mdi mdi-cog-outline"></span> General</a>
<a class="settings-tab " href="/user-guide/settings/media-management"><span class="mdi mdi-folder-cog-outline"></span> Media Management</a>
<a class="settings-tab " href="/user-guide/settings/profiles"><span class="mdi mdi-translate"></span> Profiles</a>
<a class="settings-tab " href="/user-guide/settings/providers"><span class="mdi mdi-cloud-search-outline"></span> Providers</a>
<a class="settings-tab " href="/user-guide/settings/translation"><span class="mdi mdi-brain"></span> Translation</a>
<a class="settings-tab " href="/user-guide/settings/integrations"><span class="mdi mdi-connection"></span> Integrations</a>
</div>

# Settings — API Keys

The **API Keys** page (`Settings → API Keys`) is a centralized registry for all external service credentials. Instead of hunting for keys across multiple settings tabs, you can view status, test connectivity, rotate values, and export/import all keys from one place.

---

## Registered Services

| Service | Keys Managed |
|---------|-------------|
| **Sublarr** | Internal API key (`X-Api-Key`) |
| **Sonarr** | `sonarr_api_key` |
| **Radarr** | `radarr_api_key` |
| **Jellyfin / Emby** | `api_key` per configured instance |
| **OpenSubtitles** | `opensubtitles_api_key`, username, password |
| **Jimaku** | `jimaku_api_key` |
| **SubDL** | `subdl_api_key` |
| **DeepL** | `deepl_api_key` |
| **Apprise** | Notification URLs |

Each service shows:
- **Status badge** — Configured / Unconfigured / Error
- **Masked value** — last 4 characters visible (e.g. `••••a1b2`)
- **Test button** — pings the service to verify the key works
- **Rotate button** — replaces the key and immediately invalidates all cached tokens

---

## Testing a Key

Click **Test** next to any service to verify the credentials are accepted. The result badge updates to:

- ✅ **Connected** — key is valid and the service responded successfully
- ❌ **Error** — key is set but the service returned an error (wrong key, unreachable host)
- ⚫ **Unconfigured** — no key is set for this service

Test results are live checks — they are not cached and do not affect normal Sublarr operation.

---

## Rotating a Key

**Rotate** generates a new key for services that support it (currently: Sublarr internal API key). For external services (Sonarr, Radarr, providers), rotation updates the stored value — it does not generate a new key in the external service. You must first rotate the key in the external service's settings, then paste the new value here.

After rotating, Sublarr immediately:
1. Saves the new key to the database
2. Invalidates all in-memory caches that used the old key
3. Reloads settings so the new key takes effect without a restart

---

## Export / Import

Use **Export** to download all API keys as an encrypted ZIP file. The archive contains a CSV with service names, key names, and values. This is useful for:
- Migrating to a new server
- Keeping an offline backup of credentials
- Seeding a staging environment

**Import** accepts the same ZIP format. Existing keys are overwritten; keys not present in the archive are left unchanged.

> [!WARNING]
> The export archive contains plaintext API keys. Store it like a password file — do not commit it to version control or share it publicly.

---

## Bazarr Migration

If you are migrating from Bazarr, use **Import from Bazarr** to automatically extract and import subtitle provider API keys from your Bazarr SQLite database. Supported providers: OpenSubtitles, Addic7ed, TVSubtitles.

1. Copy your Bazarr `bazarr.db` file to an accessible path inside the Sublarr container
2. Paste the path in the **Bazarr DB path** field
3. Click **Import from Bazarr** — matching keys are imported immediately

Bazarr login credentials (username/password) are imported where applicable. Provider-specific configuration (languages, min score) must be set manually in **Settings → Providers**.

---

## API Reference

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/v1/api-keys/` | List all services with status |
| `GET` | `/api/v1/api-keys/{service}` | Get detail for one service |
| `PUT` | `/api/v1/api-keys/{service}` | Update keys for a service |
| `POST` | `/api/v1/api-keys/{service}/test` | Test connectivity |
| `POST` | `/api/v1/api-keys/export` | Export all keys as ZIP |
| `POST` | `/api/v1/api-keys/import` | Import keys from ZIP |
| `POST` | `/api/v1/api-keys/import/bazarr` | Import from Bazarr DB |
