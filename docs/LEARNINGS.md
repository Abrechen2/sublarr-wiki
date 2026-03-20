# Learnings — SublarrWiki

> Lessons learned during wiki management. Add new entries at the top.

## Template

| Date | Topic | Learning |
|------|-------|----------|
| YYYY-MM-DD | Topic | What was learned |

## Entries

| Date | Topic | Learning |
|------|-------|----------|
| 2026-03-18 | Security | GraphQL pages.list exposes unpublished pages — need Wiki.js ACL config to restrict |
| 2026-03-18 | Security | nginx-wiki.conf created to block .git directory and restrict /healthz endpoint |
| 2026-03-16 | Coverage | Gap audit found ~15-20 settings fields undocumented in wiki (log_file, db_path, backoff_base, etc.) |
| 2026-03-15 | Expansion | Major wiki expansion: 30 new pages, 21 screenshots, settings sections overhauled |
| 2026-03-14 | Structure | Dual structure exists (en/ folder + root-level mirrors) — en/ is canonical, root-level is legacy |
| 2026-03-14 | Sync | Wiki.js Git sync runs every 5 minutes — never edit files directly in Git repo |
