# SublarrWiki — Claude Code Instructions

Git-synced content mirror from Wiki.js. Wiki.js syncs FROM this Git repo every 5 minutes — **editing `.md` files directly here IS the correct way to update wiki content.** Commit and push to `main`; Wiki.js picks up the changes automatically.

## Stack

| Component | Details |
|-----------|---------|
| CMS | Wiki.js with Git sync (5-minute interval) |
| Hosting | Proxmox CT 124 (192.168.178.142) on pve-node1 |
| Live URL | https://wiki.sublarr.de (Cloudflare Tunnel) |
| Reverse Proxy | nginx (`nginx-wiki.conf`) → Wiki.js on port 3000 |

## Content Structure

```
en/                           # Primary English wiki
├── home.md                   # Landing page with navigation
├── getting-started/          # Installation, quick start, env vars, FAQ, upgrade
├── user-guide/               # Features: library, wanted, translation, editor, etc.
│   └── settings/             # 20+ settings subsections
├── troubleshooting/          # General, reverse-proxy, performance
└── development/              # Architecture, API, plugins, database, contributing
```

**Note:** Legacy mirrors exist at root level (`getting-started/`, `user-guide/`, `development/`). The `en/` folder is the canonical structure.

## Editing Workflow

**Preferred (for Claude):** Edit `.md` files directly, commit, push to `main`. Wiki.js picks up changes within 5 minutes.

**Alternative (manual):** Open Wiki.js admin at `https://wiki.sublarr.de/a` and edit in the web UI — changes sync back to this repo automatically.

## Release Workflow (REQUIRED at every version bump)

At every Sublarr release, update these wiki files:

1. **`en/home.md`** — version badge line (e.g. `> **Latest:** v0.33.0-beta — Short description`)
2. **New feature pages** — add/update pages in `en/user-guide/` for any new features
3. **Settings docs** — update `en/user-guide/settings/` if new config fields were added (run `python wiki_audit_settings.py` to find gaps)
4. Commit all changes and push to `main`

## Security Findings (FINDINGS.md)

Key issues from pentest 2026-03-18:
- **H1**: GraphQL `pages.list` exposes unpublished pages without auth
- **M1**: CSP `unsafe-inline` in script-src
- **M4**: `.git` returns HTTP 500 (fixed in nginx-wiki.conf → 404)
- **L1**: `/healthz` publicly accessible (fixed in nginx-wiki.conf → internal only)

## nginx Configuration (nginx-wiki.conf)

- Blocks `.git` and hidden directories (returns 404)
- Restricts `/healthz` to RFC-1918 private IPs
- Adds `Vary: Cookie` header (BREACH mitigation)
- Proxies to Wiki.js on `http://127.0.0.1:3000`

## Documentation Gap Audits

Run from the parent directory:
```bash
python wiki_audit_settings.py   # Config fields vs. wiki coverage
python wiki_audit_features.py   # CHANGELOG features vs. wiki coverage
```

Reports: `docs/wiki-gap-report-2026-03-16.md`

## Cross-Repo Dependencies

- Documents features from `Sublarr/` — must stay in sync with releases
- Settings docs must cover all fields from `Sublarr/backend/config.py`
- Screenshots in `img/` must be updated when UI changes
- Version reference on home page must match `Sublarr/backend/VERSION`

## Dev Credentials (.env)

Local dev only (NOT production):
```
DB_USER=wiki
DB_PASS=wiki-local-dev
DB_NAME=wiki
```
