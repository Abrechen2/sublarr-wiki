---
title: Settings — Backup
description: Scheduled backup configuration, retention policy, manual backup, and database restore procedure
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

# Settings — Backup

![Settings — Backup](/img/screen-settings-backup.png)

Sublarr includes a built-in scheduled backup system for its database and configuration. Backups are stored as ZIP archives and can be restored either through the UI or manually.

## Backup Schedule

| Setting | Default | Description |
|---------|---------|-------------|
| Daily backup enabled | `true` | Run an automatic backup every day at the configured time |
| Daily backup time | `02:00` | Time of day (24-hour, local server time) at which the daily backup runs |
| Weekly backup day | `Sun` | Day of the week on which the weekly backup is created. Options: Mon, Tue, Wed, Thu, Fri, Sat, Sun |
| Monthly backup day | `1` | Day of the month (1–28) on which the monthly backup is created. Clamped to 28 to avoid issues with short months. |

Daily, weekly, and monthly backups run independently. On a day that matches both the daily and weekly schedule, both backup files are created. This is intentional — the weekly backup is a separate retention tier, not a replacement for the daily backup.

> [!NOTE]
> All scheduled backup times are interpreted in the **server's local timezone**, not UTC. If your container has `TZ` set in the environment, that timezone applies. Verify your timezone with `docker exec sublarr date` if backups appear to run at unexpected times.

---

## Retention

| Setting | Default | Description |
|---------|---------|-------------|
| Daily copies to keep | `7` | Number of most-recent daily backup files to retain. Older files are deleted automatically after each new backup. |
| Weekly copies to keep | `4` | Number of most-recent weekly backup files to retain (approximately 1 month). |
| Monthly copies to keep | `3` | Number of most-recent monthly backup files to retain (approximately 3 months). |

With the default settings, the backup directory will contain at most `7 + 4 + 3 = 14` backup ZIP files, covering a rolling window of approximately 3 months.

**Naming convention:**

```
sublarr-backup-daily-2026-03-15T02-00-00.zip
sublarr-backup-weekly-2026-03-09T02-00-00.zip
sublarr-backup-monthly-2026-03-01T02-00-00.zip
```

---

## Backup Location

| Setting | Default | Description |
|---------|---------|-------------|
| Backup directory | `/config/backups` | Directory inside the container where backup ZIP files are stored. Change this to a host-mounted volume path if you want backups to persist independently of the container's config volume. |

> [!TIP]
> Mount the backup directory to a separate volume or a network share to protect backups from being lost if the config volume is corrupted or accidentally deleted:
>
> ```yaml
> volumes:
>   - /mnt/nas/sublarr-backups:/config/backups
> ```

---

## Manual Backup

The **Create Backup Now** button (at the top of the Backup settings page) creates a backup immediately, outside the scheduled windows. The file is placed in the same backup directory as scheduled backups and follows the same naming convention, with the tag `manual`:

```
sublarr-backup-manual-2026-03-15T14-32-07.zip
```

Manual backups are **not subject to retention limits** — they are never automatically deleted. Remove them manually when no longer needed.

**What a backup ZIP contains:**

| File | Description |
|------|-------------|
| `sublarr.db` | Full SQLite database export — all subtitle records, library entries, provider history, and settings stored in the database |
| `config-export.json` | Human-readable JSON export of all UI-configurable settings (equivalent to the output of `GET /api/v1/config/export`). Does not include secrets (API keys, passwords). |
| `backup-manifest.json` | Metadata: Sublarr version, backup timestamp, database schema version, file checksums |

> [!NOTE]
> Subtitle files themselves (`.srt`, `.ass` on disk) are **not included** in the backup ZIP. Only the database records pointing to those files are backed up. To back up the actual subtitle files, include the media directory in your host-level backup strategy.

---

## Restore

### Restore via UI

1. Go to **Settings → Backup**.
2. The **Backup Files** table lists all ZIP files in the backup directory, sorted by date.
3. Click **Restore** next to the backup you want to restore.
4. Confirm the warning dialog — all data added after the backup date will be lost.
5. Sublarr will replace the active database with the one from the ZIP and restart automatically.

### Manual Restore

If the web UI is not accessible (e.g. after a failed migration):

1. **Stop the Sublarr container:**
   ```bash
   docker compose stop sublarr
   ```

2. **Extract the database from the backup ZIP:**
   ```bash
   unzip sublarr-backup-daily-2026-03-15T02-00-00.zip sublarr.db -d /tmp/restore/
   ```

3. **Replace the active database:**
   ```bash
   cp /tmp/restore/sublarr.db /path/to/config/sublarr.db
   ```

4. **Restart the container:**
   ```bash
   docker compose start sublarr
   ```

5. Verify the restore by checking the library — the media count and most recent subtitle timestamps should match the backup date.

> [!WARNING]
> Restoring a backup overwrites the current database. All subtitle records and settings added after the backup date will be lost. If unsure, create a manual backup of the current state before restoring an older one.
