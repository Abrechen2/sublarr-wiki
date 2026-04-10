---
title: Cleanup
description: Automated cleanup operations for subtitle sidecar files and database entries
published: true
date: 2026-04-10
---

# Cleanup

The Cleanup page (**Settings → Cleanup**) provides five fixed automated operations that remove unnecessary subtitle files and database entries from your library.

## Operations

### Language Filter
Deletes subtitle sidecar files in languages not on your keep-list. Configure which languages to retain — all others are removed.

**Config:** Comma-separated list of language codes to keep (e.g. `de,en`)

### Format Upgrade
Deletes SRT sidecar files when an ASS version of the same subtitle already exists for the same episode. Keeps your library free of lower-quality duplicates.

### Orphan Files
Deletes subtitle sidecar files that have no matching video file in the same folder. Catches leftover sidecars after media is moved or deleted.

### Orphan DB Entries
Removes database entries whose subtitle file no longer exists on disk. Keeps the database consistent with the actual file system state.

### Old Backups
Deletes remux backup files (`.mkv.bak`) after the configured retention period expires. Controlled by the `remux_backup_retention_days` setting.

## Using Cleanup Operations

Each operation is an expandable card with:
- **Toggle** — enable or disable the operation
- **Configuration** — operation-specific settings (e.g. language list)
- **Schedule** — manual / daily / weekly / after scan
- **Preview** — dry run showing up to 20 example files that would be affected, with reason
- **Run Now** — execute immediately

## Dry-Run Preview

Before running any operation, use **Preview** to see which files would be affected. The preview shows:
- File path
- File size
- Reason for deletion (e.g. `lang:ja`, `replaced by Episode.S01E01.de.ass`, `no video in folder`)

No files are deleted during preview.

## Deduplication

The **Deduplication** section below the five operations uses SHA-256 hashing to find identical subtitle files across your library. Run a scan, then choose which copy to keep for each duplicate group.

## Cleanup History

Past cleanup runs appear in the **History** table at the bottom of the page, showing files processed, files deleted, and bytes freed for each run.
