# SublarrWiki — Claude Code Instructions

Git-synced content mirror from Wiki.js. **Do NOT edit files directly in this repo.** Edit pages in the Wiki.js admin — changes sync automatically every 5 minutes.

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

1. Open Wiki.js admin at `https://wiki.sublarr.de/a` (secured with 403 for non-admins)
2. Edit or create pages in the web UI
3. Changes auto-sync to this Git repo within 5 minutes
4. Review changes via `git log` or `git diff`

**Never:** Edit `.md` files directly in this repo — they will be overwritten on next sync.

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
