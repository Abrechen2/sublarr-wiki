# Security Pentest Findings — sublarr.de / wiki.sublarr.de

**Datum:** 2026-03-18
**Tester:** Kali Linux (intern, via S2S-VPN)
**Tools:** rustscan, nmap, nikto, testssl.sh, wafw00f, feroxbuster, katana, dalfox, gau, sublist3r, arjun, whatweb, mitmproxy, graphw00f, gospider

---

## Infrastruktur-Übersicht

| Eigenschaft | Wert |
|-------------|------|
| WAF | Cloudflare |
| IPs | 104.21.22.96, 172.67.204.40 (Cloudflare Proxy) |
| TLS | TLS 1.2 + 1.3, Let's Encrypt (E7), ECDSA P-256 |
| Stack | sublarr.de: Static HTML / sublarr.de: nginx via Cloudflare |
| Wiki | Wiki.js (Node.js, GraphQL API, PostgreSQL) |
| CDN | Cloudflare — echte Origin-IP nicht ermittelbar |

---

## Findings

### 🔴 HIGH

#### H1 — GraphQL Information Disclosure: Unlisted Pages via `pages.list`

**Endpoint:** `POST https://wiki.sublarr.de/graphql`
**Authentifizierung:** Keine
**CVSS (v3.1):** 5.3 (Medium-High) — AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N

**Beschreibung:**
Die GraphQL-Query `pages.list` gibt ohne Authentifizierung alle 57 veröffentlichten Wiki-Seiten zurück — inklusive 3 intern gehaltener Seiten, die nicht im Sidebar verlinkt sind:

| ID | Titel | Pfad |
|----|-------|------|
| 29 | Subtitle Provider System | `content/en/development/providers` |
| 30 | Subtitle Scoring Algorithm | `content/en/development/scoring` |
| 31 | Wiki Expansion Design Spec | `docs/superpowers/specs/2026-03-15-wiki-expansion-design` |

Diese Seiten sind über direkte URL öffentlich abrufbar (HTTP 200), obwohl sie im Navigation-Sidebar nicht auftauchen.

**Reproduktion:**
```bash
curl -s https://wiki.sublarr.de/graphql -X POST \
  -H 'Content-Type: application/json' \
  -d '{"query":"{pages{list{id,title,path,isPublished}}}"}'
# → Gibt alle Seiten inkl. interner zurück

# Direkter Zugriff auf nicht-verlinkte Seite:
curl -s https://wiki.sublarr.de/docs/superpowers/specs/2026-03-15-wiki-expansion-design
# → HTTP 200, Inhalt der internen Design-Spec sichtbar
```

**Empfehlung:**
- Interne/Draft-Seiten in Wiki.js als "Private" oder "Unpublished" markieren
- GraphQL `pages.list` auf authentifizierte Nutzer beschränken (Wiki.js Admin → GraphQL ACL)
- Alternativ: Interne Seiten in ein separates, nicht-öffentliches Wiki auslagern

---

### 🟠 MEDIUM

#### M1 — CSP `unsafe-inline` in `script-src`

**Header:** `Content-Security-Policy`
**Betroffene Domain:** sublarr.de

**Beschreibung:**
Die Content Security Policy enthält `'unsafe-inline'` in der `script-src`-Direktive:

```
Content-Security-Policy: default-src 'self';
  script-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  ...
```

`unsafe-inline` erlaubt die Ausführung beliebigen Inline-JavaScripts und schwächt den XSS-Schutz erheblich. Ein Angreifer der XSS-Input einschleusen kann, würde nicht durch die CSP geblockt.

**Empfehlung:**
- `'unsafe-inline'` entfernen
- Inline-Scripts mit CSP-Nonces ersetzen:
  ```
  script-src 'self' 'nonce-{RANDOM}' https://fonts.googleapis.com;
  ```
- Oder Hash-basiert: `'sha256-{HASH_DES_SCRIPTS}'`

---

#### M2 — BREACH Vulnerability (HTTP Kompression + HTTPS)

**Betroffene Domain:** wiki.sublarr.de
**CVE:** CVE-2013-3587

**Beschreibung:**
HTTP-Kompression (gzip/brotli) ist auf dynamischen Seiten aktiv. In einem MITM-Szenario (z.B. kompromittiertes Netzwerk) kann ein Angreifer durch wiederholte Requests schrittweise Secrets aus komprimierten HTTPS-Responses ableiten (z.B. CSRF-Tokens).

```
Content-Encoding: deflate
```

**Empfehlung:**
- CSRF-Tokens nicht in komprimierte Response-Bodies einbetten
- `Vary: Cookie` Header setzen, um Caching-basierte Angriffe zu erschweren
- Oder Kompression für sensitive Endpoints deaktivieren

---

#### M3 — Veraltete CBC-Ciphers in TLS 1.2

**Betroffene Domain:** sublarr.de / wiki.sublarr.de

**Beschreibung:**
TLS 1.2 bietet neben modernen AEAD-Ciphers noch veraltete CBC-basierte Ciphers an:

```
ECDHE-ECDSA-AES128-SHA256
ECDHE-ECDSA-AES128-SHA
ECDHE-ECDSA-AES256-SHA384
ECDHE-ECDSA-AES256-SHA
```

Dies macht LUCKY13-Angriffe (CVE-2013-0169) theoretisch möglich.

**Empfehlung:**
- In Cloudflare SSL/TLS-Einstellungen: "Modern" Cipher Suite wählen
- Nur AEAD-Ciphers erlauben: `TLS_AES_*_GCM_*`, `CHACHA20_POLY1305`

---

#### M4 — `.git` Verzeichnis antwortet mit HTTP 500 statt 404

**Betroffene Domain:** wiki.sublarr.de

**Beschreibung:**
Die folgenden Pfade geben HTTP 500 zurück statt HTTP 404:

```
/.git/HEAD      → 500 Internal Server Error
/.git/config    → 500 Internal Server Error
/.git/index     → 500 Internal Server Error
```

HTTP 500 (statt 404) bestätigt, dass das `.git`-Verzeichnis auf dem Server vorhanden ist (Wiki.js Git-Sync). Aktuell ist der Inhalt nicht abrufbar, aber das Verhalten ist ein Indikator. Wenn die Blockierung je aufgehoben wird, wäre ein vollständiger Source-Code-Leak möglich.

**Empfehlung:**
- `.git`-Verzeichnis explizit über Cloudflare-WAF-Regel oder nginx blockieren:
  ```nginx
  location ~ /\.git {
      deny all;
      return 404;
  }
  ```

---

### 🟡 LOW

#### L1 — `/healthz` Endpoint öffentlich zugänglich

**URL:** `https://wiki.sublarr.de/healthz`
**Response:** `{"ok":true}`

**Beschreibung:**
Der Health-Check-Endpoint ist ohne Authentifizierung erreichbar und bestätigt den Betrieb des Services. Kein direktes Risiko, aber unnötige Information.

**Empfehlung:** Endpoint auf interne IPs beschränken oder via Cloudflare-Regel sperren.

---

#### L2 — OCSP Stapling nicht aktiv

**Betroffene Domain:** sublarr.de / wiki.sublarr.de

**Beschreibung:**
OCSP Stapling ist nicht konfiguriert. Browser müssen OCSP-Anfragen direkt an Let's Encrypt stellen — führt zu Privacy-Leakage und leichten Performance-Einbußen.

**Empfehlung:**
```nginx
ssl_stapling on;
ssl_stapling_verify on;
```

---

#### L3 — DNS CAA-Record fehlt

**Domain:** sublarr.de

**Beschreibung:**
Kein DNS CAA-Record konfiguriert. Jede Certificate Authority kann theoretisch Zertifikate für sublarr.de ausstellen.

**Empfehlung:**
```dns
sublarr.de. CAA 0 issue "letsencrypt.org"
sublarr.de. CAA 0 issuewild ";"
```

---

#### L4 — `Permissions-Policy` Header doppelt gesetzt

**Betroffene Domain:** sublarr.de

**Beschreibung:**
Der `Permissions-Policy` Header erscheint zweimal in der HTTP-Response. Abhängig vom Browser kann der zweite Header den ersten überschreiben oder zu unerwartetem Verhalten führen.

**Empfehlung:** Doppelten Header-Eintrag aus nginx/Cloudflare-Konfiguration entfernen.

---

## Was gut ist ✓

| Kontrolle | Status |
|-----------|--------|
| HSTS (`max-age=31536000; includeSubDomains; preload`) | ✅ |
| `X-Frame-Options: DENY` | ✅ |
| `X-Content-Type-Options: nosniff` | ✅ |
| `Referrer-Policy: strict-origin-when-cross-origin` | ✅ |
| TLS 1.0 / 1.1 deaktiviert | ✅ |
| SSLv2 / SSLv3 nicht angeboten | ✅ |
| Forward Secrecy aktiv | ✅ |
| GraphQL Introspection deaktiviert | ✅ |
| User-Enumeration via Auth nicht möglich | ✅ |
| Registrierung deaktiviert | ✅ |
| Admin-Panel `/a` mit 403 gesperrt | ✅ |
| Cloudflare WAF aktiv | ✅ |
| `X-XSS-Protection: 1; mode=block` | ✅ |

---

## Priorisierung der Fixes

| Priorität | Finding | Aufwand | Status |
|-----------|---------|---------|--------|
| 1 | H1 — GraphQL pages.list einschränken | Niedrig (Wiki.js Admin) | ⚠️ Teilweise — Seiten auf `published: false` gesetzt; GraphQL ACL muss im Wiki.js Admin gesetzt werden |
| 2 | M1 — CSP unsafe-inline entfernen | Mittel (Code-Änderung) | ✅ Behoben — `nginx.conf` in SublarrWeb erstellt, `unsafe-inline` entfernt |
| 3 | L3 — DNS CAA-Record setzen | Sehr niedrig (DNS-Eintrag) | ⏳ Offen — DNS-Eintrag in Cloudflare setzen |
| 4 | M4 — .git via nginx blockieren | Niedrig (Config) | ✅ Behoben — `nginx-wiki.conf` mit `location ~ /\.git { deny all; return 404; }` |
| 5 | M3 — CBC-Ciphers deaktivieren | Niedrig (Cloudflare SSL) | ⏳ Offen — In Cloudflare SSL/TLS → "Modern" Cipher Suite wählen |
| 6 | L2 — OCSP Stapling aktivieren | Niedrig (nginx Config) | ⏳ Offen — Greift nur wenn TLS direkt auf nginx terminiert (aktuell Cloudflare Tunnel) |
| 7 | L1 — /healthz einschränken | Niedrig (CF-Regel) | ✅ Behoben — `nginx-wiki.conf` beschränkt `/healthz` auf RFC-1918 IPs |
| 8 | L4 — Doppelten Header entfernen | Sehr niedrig | ✅ Behoben — `Permissions-Policy` nur noch in `nginx.conf` gesetzt; Cloudflare Transform Rule prüfen |

### Noch manuell erforderlich

1. **Wiki.js Admin → GraphQL ACL:** `pages.list` auf authentifizierte Nutzer beschränken
2. **Cloudflare DNS:** CAA-Record hinzufügen (`letsencrypt.org`, Wildcard `";"`)
3. **Cloudflare SSL/TLS → Edge Certificates → Minimum TLS Version / Cipher Suite:** "Modern" wählen

---

---

# Interner Pentest — Sublarr Docker App (`http://192.168.178.194:5765/`)

**Datum:** 2026-03-18
**Tester:** Kali Linux (192.168.178.177, intern)
**Scope:** Interne Sublarr-Instanz (CT101, Proxmox LXC pve-node1), nicht öffentlich erreichbar
**Tools:** curl, feroxbuster, Python

---

## Infrastruktur-Übersicht

| Eigenschaft | Wert |
|-------------|------|
| Host | CT101, IP 192.168.178.194, Port 5765 |
| Stack | Gunicorn (Python), React SPA (Vite), Socket.io, PostgreSQL, Redis |
| Version | 0.30.0-beta |
| Auth | API-Key (`X-Api-Key` Header), gespeichert in `localStorage` als `sublarr_api_key` |
| Transport | HTTP (kein TLS intern) |

---

## Findings

### 🔴 KRITISCH

#### K1 — Datenbank-Credentials im API-Response exponiert

**Endpoint:** `GET /api/v1/config`
**Authentifizierung:** API-Key erforderlich

**Beschreibung:**
Der `/api/v1/config` Endpunkt gibt die vollständige PostgreSQL-Connection-String mit Passwort im Klartext zurück:

```json
"database_url": "postgresql://sublarr:efAzavFcd1VdOXt878F-dcm0JU0@sublarr-postgres:5432/sublarr"
```

Andere Secrets (API-Keys für Sonarr, Radarr, Jimaku, etc.) werden korrekt als `***configured***` maskiert — `database_url` jedoch nicht.

**Empfehlung:**
- `database_url` in der Config-Serialisierung ebenfalls maskieren: `***configured***`
- Oder das Feld komplett aus der API-Antwort entfernen

---

#### K2 — Alle Credentials in Plaintext in Host-Datei `/opt/sublarr/sublarr.env`

**Dateipfad:** `/opt/sublarr/sublarr.env` (auf dem LXC-Host)

**Beschreibung:**
Die `.env`-Datei enthält alle Secrets im Klartext:

| Secret | Beschreibung |
|--------|--------------|
| `SUBLARR_DATABASE_URL` | PostgreSQL-Passwort |
| `SUBLARR_API_KEY` | Sublarr Master-API-Key |
| `SUBLARR_SONARR_API_KEY` | Sonarr-Zugang |
| `SUBLARR_RADARR_API_KEY` | Radarr-Zugang |
| `SUBLARR_JIMAKU_API_KEY` | Provider-API-Key |
| `SUBLARR_OPENSUBTITLES_API_KEY` | Provider-API-Key |
| `SUBLARR_OPENSUBTITLES_USERNAME` | Benutzername |
| `SUBLARR_OPENSUBTITLES_PASSWORD` | Passwort im Klartext |
| `SUBLARR_SUBDL_API_KEY` | Provider-API-Key |

**Empfehlung:**
- Datei-Permissions auf `600` (nur root lesbar): `chmod 600 /opt/sublarr/sublarr.env`
- Langfristig: Docker Secrets oder Vault für Secret Management nutzen

---

### 🔴 HIGH

#### H1 — Socket.io akzeptiert unauthentifizierte Verbindungen

**Endpoint:** `GET /socket.io/?EIO=4&transport=polling`
**Authentifizierung:** Keine

**Beschreibung:**
Der Socket.io-Endpunkt gibt ohne API-Key eine Session-ID und Verbindungsparameter zurück:

```
0{"sid":"NHbsBFZGhCqUGD--AAAB","upgrades":["websocket"],"pingTimeout":20000,...}
```

Ein Angreifer mit Netzwerkzugang kann sich mit dem Socket.io-Namespace verbinden und Echtzeit-Events (Job-Status, Download-Events, Fehler-Meldungen) mitlesen — ohne sich zu authentifizieren.

**Empfehlung:**
- Socket.io-Middleware einbinden, die `X-Api-Key` vor der Verbindung validiert
- Beispiel (Python-SocketIO):
  ```python
  @sio.event
  def connect(sid, environ, auth):
      if not auth or auth.get("api_key") != VALID_KEY:
          raise ConnectionRefusedError("Unauthorized")
  ```

---

#### H2 — SSRF via konfigurierbare URL-Felder (Server-Side Request Forgery)

**Endpoint:** `PUT /api/v1/config`
**Authentifizierung:** API-Key erforderlich

**Beschreibung:**
URL-Felder in der Konfiguration (`sonarr_url`, `radarr_url`, `ollama_url`, `jellyfin_url`) können auf beliebige interne oder externe Hosts gesetzt werden. Der Server macht dann Requests zu diesen URLs (z.B. für Health-Checks via `/api/v1/integrations/health/all`).

**Reproduktion:**
```bash
# SSRF-Payload setzen:
curl -X PUT -H "X-Api-Key: $KEY" -H "Content-Type: application/json" \
  -d '{"sonarr_url":"http://169.254.169.254/latest/meta-data"}' \
  http://192.168.178.194:5765/api/v1/config

# Server macht Request zu 169.254.169.254 sobald integrations/health aufgerufen wird
curl -H "X-Api-Key: $KEY" http://192.168.178.194:5765/api/v1/integrations/health/all
```

**Empfehlung:**
- URL-Validierung: Nur HTTP/HTTPS erlauben, private RFC-1918-Ranges blockieren (`127.x`, `169.254.x`, `10.x`, `192.168.x`)
- Oder Allowlist für bekannte Hosts

---

#### H3 — Kein Rate-Limiting auf API-Endpunkten

**Beschreibung:**
Alle API-Endpunkte akzeptieren unbegrenzte Requests ohne Drosselung. API-Key-Brute-Force ist möglich. Keine `429 Too Many Requests`-Antworten beobachtet.

**Empfehlung:**
- Rate-Limiting via Flask-Limiter einbauen (z.B. 60 Requests/Minute pro IP)
- Exponential Backoff bei wiederholten 401-Fehlern

---

### 🟠 MEDIUM

#### M1 — Keine Security-Header auf API-Responses

**Beschreibung:**
Der Gunicorn-Server liefert keine sicherheitsrelevanten HTTP-Header:

```
HTTP/1.1 200 OK
Server: gunicorn          ← Version-Disclosure
Content-Type: application/json
Vary: Cookie
(keine weiteren Security-Header)
```

Fehlende Header: `X-Content-Type-Options`, `X-Frame-Options`, `Strict-Transport-Security`, `Content-Security-Policy`, `Referrer-Policy`

**Empfehlung:**
- Security-Header via Flask `after_request`-Hook oder Reverse-Proxy setzen
- `server_tokens off` / Custom Server-Header

---

#### M2 — DB-Schema-Informationen in API-Logs exponiert

**Endpoint:** `GET /api/v1/logs`
**Authentifizierung:** API-Key erforderlich

**Beschreibung:**
Interne PostgreSQL-Fehlermeldungen werden im `/api/v1/logs` Endpunkt sichtbar:

```
(psycopg2.errors.UndefinedColumn) column glossary_entries.term_type does not exist
(psycopg2.errors.UndefinedFunction) function date(unknown, unknown) does not exist
```

Tabellen- und Spaltennamen der Datenbank werden dadurch offenbart.

**Empfehlung:**
- Datenbank-Fehler intern loggen, aber in API-Logs generische Fehlermeldungen zurückgeben
- Exception-Handler einbauen, der DB-spezifische Details strippt

---

#### M3 — Kein CSRF-Schutz auf State-Changing Endpoints

**Beschreibung:**
PUT/POST/DELETE-Requests erfordern kein CSRF-Token. Authentifizierung erfolgt ausschließlich über `X-Api-Key`-Header — da der Header von Browsern nicht automatisch mitgesendet wird, ist das CSRF-Risiko gering, aber ein CORS-Misconfiguration könnte es ausnutzbar machen.

**Aktueller Status:** Gering (CORS eingeschränkt auf `http://localhost:5173,http://localhost:5765,http://192.168.178.194:5765`)

---

### 🟡 LOW

#### L1 — `/api/v1/health` ohne Authentifizierung (Information Disclosure)

**URL:** `http://192.168.178.194:5765/api/v1/health`

**Response:**
```json
{
  "services": {"media_servers": "none configured", "ollama": "OK", "providers": "error", "radarr": "OK", "sonarr": "OK"},
  "status": "healthy",
  "version": "0.30.0-beta"
}
```

Offenbart: Versions-Info, verbundene Services, Service-Status — ohne Auth.

**Empfehlung:** Endpoint auf interne Monitoring-Systeme beschränken oder Auth hinzufügen.

---

#### L2 — Server-Version-Disclosure (`Server: gunicorn`)

Der `Server`-Header enthält den Server-Namen. Kombiniert mit der Version aus `/api/v1/health` und `/api/v1/update` (GitHub-URL) ist die vollständige Stack-Info ermittelbar.

**Empfehlung:** Custom Server-Header setzen oder via Proxy entfernen.

---

#### L3 — `opensubtitles_username` im Config-Response ungemaskiert

In `/api/v1/config` wird `opensubtitles_username: "abrechen2"` im Klartext zurückgegeben, während das Passwort maskiert ist. Der Benutzername ist für Account-Enumeration nutzbar.

**Empfehlung:** Auch `opensubtitles_username` maskieren.

---

#### L4 — Kein TLS intern (HTTP-only)

Der interne Service läuft ohne TLS auf Port 5765. Im internen Netzwerk könnte ein kompromittierter Host den API-Key im Netzwerktraffic abfangen.

**Empfehlung:** TLS via Reverse Proxy (Nginx) auch intern einsetzen, oder mTLS zwischen Komponenten.

---

## Was gut ist ✓

| Kontrolle | Status |
|-----------|--------|
| API-Key-Auth auf allen `/api/v1/*`-Endpunkten (außer health) | ✅ |
| Sensitive API-Keys korrekt maskiert (`***configured***`) für Jimaku, Sonarr, Radarr, etc. | ✅ |
| CORS auf bekannte Origins beschränkt | ✅ |
| SQL-Injection durch parametrisierte Queries verhindert | ✅ |
| Kein Default-Passwort / Passwort-Auth nicht erforderlich | ✅ |
| Rate-Limit auf File-Upload (max_payload in Socket.io: 1MB) | ✅ |
| Automatische Backups aktiv (`/config/backups/`) | ✅ |

---

## Priorisierung der Fixes (Interne App)

| Priorität | Finding | Aufwand |
|-----------|---------|---------|
| 1 | K1 — `database_url` in Config-Response maskieren | Sehr niedrig (1 Zeile) |
| 2 | K2 — `sublarr.env` auf `chmod 600` setzen | Sehr niedrig (1 Befehl) |
| 3 | H1 — Socket.io Auth-Middleware | Niedrig (10-20 Zeilen) |
| 4 | H2 — SSRF: URL-Validierung in config-Feldern | Mittel |
| 5 | H3 — Rate-Limiting via Flask-Limiter | Niedrig |
| 6 | M1 — Security-Header hinzufügen | Niedrig |
| 7 | M2 — DB-Fehler aus API-Logs filtern | Niedrig |
| 8 | L3 — opensubtitles_username maskieren | Sehr niedrig |
4. **Cloudflare Transform Rules:** Doppelten `Permissions-Policy` Header entfernen (falls Cloudflare ihn zusätzlich setzt)
5. **nginx-wiki.conf deployen:** Nach CT 124 unter `/etc/nginx/sites-available/wiki.sublarr.de` einspielen
