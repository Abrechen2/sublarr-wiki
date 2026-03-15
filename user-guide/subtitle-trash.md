---
title: Subtitle Trash
description: Soft-delete for subtitle files — deleted subtitles are moved to a trash folder and can be restored within the retention period.
published: true
date: 2026-03-15
tags: user-guide
editor: markdown
dateCreated: 2026-03-15
---

# Subtitle Trash

Subtitles deleted in Sublarr are not immediately removed from disk. They are moved to a trash folder and can be restored within a configurable retention period.

## Overview

Sublarr uses a soft-delete model for subtitle files. When you delete a subtitle — whether manually from the episode panel or as a side effect of an upgrade, sync, or tool run — the file is never deleted outright. Instead it is moved to Sublarr's internal trash directory and a deletion timestamp is recorded. You can browse trashed files, restore any of them to their original location, or permanently delete them. Files that are older than the configured retention period are automatically purged by the daily cleanup job.

This protects against accidental data loss from subtitle operations that are difficult to undo, such as HI removal, credit filtering, or sync runs that produced a worse result than the original.

## How Trash Works

When a subtitle is deleted or superseded, Sublarr:

1. Moves the file to `/config/trash/<YYYY-MM-DD>/<original-relative-path>`.
2. Records the original path and deletion timestamp in the database.
3. Marks the subtitle record as deleted in the library — the episode shows no assigned subtitle.

The media directory itself is never written to during a delete. If you navigate to the episode's media folder via a file browser, the `.ass` or `.srt` file will simply be absent — it has been moved, not deleted in place.

## Viewing Trashed Files

1. Go to **Settings → Automation → Subtitle Trash**.
2. The trash list shows all trashed subtitle files with:
   - Original file path
   - Series and episode association
   - Deletion timestamp
   - Days remaining before auto-purge

You can filter by series name or date range using the search field at the top of the list.

## Restoring from Trash

1. In the trash list, find the file you want to restore.
2. Click **Restore**.
3. Sublarr moves the file back to its original path and reactivates the database record.
4. The episode in the Library view will show the subtitle as assigned again.

If the original path no longer exists (e.g. the episode file was moved or renamed), the restore will fail. In that case, you can copy the path shown in the trash list to locate the file in `/config/trash/` and move it manually.

## Manual Deletion

To permanently delete a trashed file before the retention period expires:

1. In the trash list, click **Delete Permanently** next to the file.
2. Confirm the dialog.

Permanent deletion cannot be undone.

## Auto-Purge

Sublarr runs a daily cleanup job that permanently deletes any trashed files older than the configured retention period.

| Setting | Environment Variable | Default |
|---------|---------------------|---------|
| Retention period | `SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS` | `7` days |

Files are purged at midnight (container local time) if their deletion timestamp is older than the retention value.

To change the retention period, set the environment variable in your `docker-compose.yml`:

```yaml
environment:
  SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS: "14"
```

Then restart the container for the change to take effect.

## Disabling Trash

Soft-delete cannot be turned off as a feature — it is part of Sublarr's core subtitle management model. However, if you want deletions to be effectively permanent, set the retention to `0`:

```yaml
environment:
  SUBLARR_SUBTITLE_TRASH_RETENTION_DAYS: "0"
```

With a retention of `0`, the daily cleanup job will purge all trashed files at midnight of the day they were deleted. This is equivalent to disabling soft-delete for most practical purposes, but files deleted during the current day can still be restored until midnight.

> [!TIP]
> The default 7-day retention is a deliberate safety net. Before reducing it, consider how often you use subtitle tools like HI removal or video sync — those operations always back up the original to trash, and 7 days gives you enough time to notice a bad result and restore.
