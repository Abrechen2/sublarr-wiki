---
title: Settings — Security
description: API key, web UI authentication, session settings, and CORS configuration
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

# Settings — Security

![Settings — Security](/img/screen-settings-security.png)

Sublarr's security model has three independent layers: an **API key** for machine-to-machine access, **web UI authentication** for browser sessions, and **CORS** to control which origins can make cross-origin requests. For most homelab setups on a trusted local network, the defaults are acceptable. When exposing Sublarr to the internet via a reverse proxy, all three layers should be enabled.

> **Security checklist for internet-exposed deployments:**
> - Enable API key authentication
> - Enable web UI login with a strong password
> - Restrict CORS to your domain
> - Terminate TLS at the reverse proxy — never expose port 5765 directly
> - See [Reverse Proxy Guide](/troubleshooting/reverse-proxy) for nginx and Caddy examples

---

## API Key

The API key protects all REST API endpoints (`/api/v1/*`). When set, every request must include the key in the `X-Api-Key` HTTP header. Requests without a valid key receive a `401 Unauthorized` response.

This is the same key referenced in **Settings → General → API Key Authentication** — both locations show and edit the same value.

| Setting | Default | Description |
|---------|---------|-------------|
| **API Key** | *(empty)* | Static bearer token for API access. When empty, the API is unauthenticated. |

### Generating a Key

Use a password manager or generate one from a terminal:

```bash
openssl rand -hex 32
# Example output: a3f8b2c1d94e5f6071829a3b4c5d6e7f8091a2b3c4d5e6f7081920a1b2c3d4e5
```

Once set, pass the key in all API calls:

```http
GET /api/v1/series
X-Api-Key: a3f8b2c1d94e5f6071829a3b4c5d6e7f8091a2b3c4d5e6f7081920a1b2c3d4e5
```

Integrations configured in **Settings → Integrations** (Sonarr, Radarr, Jellyfin) automatically include the API key in their callback requests if you paste it into the integration setup fields.

---

## Web UI Authentication

Web UI authentication is a separate session-based login layer for the browser interface. It does not affect API access — that is controlled by the API key.

| Setting | Default | Description |
|---------|---------|-------------|
| **Enable login** | `false` | When enabled, the browser redirects to a login page on first visit |
| **Username** | `admin` | Login username |
| **Change Password** | — | Button — prompts for current password, then new password + confirmation |

**Enabling authentication for the first time:**

1. Navigate to **Settings → Security → Web UI Authentication**
2. Set a strong password via **Change Password** before enabling login
3. Toggle **Enable login** to on
4. Click **Save**
5. The next page load redirects to `/login`

**Recovery if locked out:** Set `SUBLARR_DISABLE_AUTH=true` in your `.env` file and restart the container. This bypasses UI authentication for one session, allowing you to reset the password. Remove the variable and restart again afterwards.

---

## Session Settings

These settings control how long browser sessions remain valid.

| Setting | Default | Description |
|---------|---------|-------------|
| **Session lifetime** | `168` hours (7 days) | How long a session cookie is valid after the last activity |
| **Remember me duration** | `30` days | Extended lifetime when the user checks "Remember me" on the login page |

Reducing the session lifetime increases security at the cost of requiring more frequent logins. For a single-user homelab instance, the defaults are reasonable. For shared or multi-user deployments, consider shorter values.

Sessions are stored server-side. Logging out invalidates the session immediately regardless of the lifetime setting.

---

## CORS

Cross-Origin Resource Sharing (CORS) controls which web origins are permitted to make API requests to Sublarr from a browser. This primarily matters if you have a custom web frontend or integration that runs on a different domain.

| Setting | Default | Description |
|---------|---------|-------------|
| **Allowed Origins** | `*` | Comma-separated list of permitted origins. `*` allows all origins. |

**Examples:**

```
# Allow all origins (default — fine for local network)
*

# Restrict to your reverse-proxy domain
https://sublarr.yourdomain.com

# Allow multiple specific origins
https://sublarr.yourdomain.com, https://jellyfin.yourdomain.com
```

When `Allowed Origins` is set to anything other than `*`, Sublarr also enforces `Access-Control-Allow-Credentials: true` for authenticated sessions. Origins must be exact matches — wildcards within a value (e.g. `https://*.yourdomain.com`) are not supported.

> **Note:** CORS only applies to browser-originated requests. Server-to-server API calls (from n8n, Home Assistant, etc.) are not subject to CORS restrictions.

---

## Security Recommendations

The checklist below covers the most important steps before exposing Sublarr outside a trusted local network:

- **Enable API key** — prevents unauthenticated access to all `/api/v1/` endpoints
- **Enable web UI login** — protects the browser interface with a username and password
- **Set a strong password** — at least 20 characters; use a password manager
- **Restrict CORS** — set Allowed Origins to your exact reverse-proxy domain
- **Use HTTPS** — terminate TLS at a reverse proxy (nginx, Caddy, NPM); never expose port `5765` directly to the internet
- **Do not store the API key in browser history** — use a password manager or secret manager to retrieve it when needed
- **Rotate the API key periodically** — update it in Sublarr and in all connected integrations

See the [Reverse Proxy Guide](/troubleshooting/reverse-proxy) for ready-to-use configuration snippets for nginx and Caddy.
