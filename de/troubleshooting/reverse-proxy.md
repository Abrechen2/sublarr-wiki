---
title: Reverse-Proxy-Leitfaden
description: Sublarr hinter nginx, Caddy oder NPM einrichten
published: true
date: 2026-03-14
---

# Reverse-Proxy-Einrichtung

Sublarr läuft auf Port 5765 und funktioniert hinter jedem Reverse Proxy. Dieser Leitfaden behandelt Nginx und Traefik.

---

## Nginx

### Einfache Einrichtung (HTTP)

```nginx
server {
    listen 80;
    server_name sublarr.example.com;

    location / {
        proxy_pass http://sublarr:5765;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # WebSocket-Unterstützung (erforderlich für Echtzeit-Updates)
    location /socket.io/ {
        proxy_pass http://sublarr:5765/socket.io/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_read_timeout 86400;
    }
}
```

### Mit SSL (Certbot / Let's Encrypt)

```nginx
server {
    listen 443 ssl;
    server_name sublarr.example.com;

    ssl_certificate     /etc/letsencrypt/live/sublarr.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/sublarr.example.com/privkey.pem;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;

    # Timeouts für große Untertitel-Datei-Uploads erhöhen
    client_max_body_size 50M;
    proxy_read_timeout 300s;
    proxy_send_timeout 300s;

    location / {
        proxy_pass http://sublarr:5765;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }

    location /socket.io/ {
        proxy_pass http://sublarr:5765/socket.io/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto https;
        proxy_read_timeout 86400;
    }
}

# HTTP -> HTTPS Weiterleitung
server {
    listen 80;
    server_name sublarr.example.com;
    return 301 https://$host$request_uri;
}
```

### Unterpfad-Einrichtung

```nginx
location /sublarr/ {
    proxy_pass http://sublarr:5765/;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Prefix /sublarr;
    proxy_set_header X-Forwarded-Proto $scheme;
}

location /sublarr/socket.io/ {
    proxy_pass http://sublarr:5765/socket.io/;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_read_timeout 86400;
}
```

---

## Traefik v2 / v3

### Docker Labels

```yaml
services:
  sublarr:
    image: ghcr.io/abrechen2/sublarr:0.11.0-beta
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.sublarr.rule=Host(`sublarr.example.com`)"
      - "traefik.http.routers.sublarr.entrypoints=websecure"
      - "traefik.http.routers.sublarr.tls.certresolver=letsencrypt"
      - "traefik.http.services.sublarr.loadbalancer.server.port=5765"
      - "traefik.http.middlewares.sublarr-ws.headers.customrequestheaders.X-Forwarded-Proto=https"
```

---

## Authentik / Authelia (SSO)

Um SSO für die API (Skripte) zu umgehen:

```yaml
bypass:
  - domain: sublarr.example.com
    resources:
      - "^/api/v1/.*$"
```

**Wichtig:** `/socket.io/` immer von der SSO-Forward-Auth ausschließen — SSO-Middleware entfernt häufig WebSocket-Upgrade-Header.

---

## API-Key-Authentifizierung

Wenn Sublarr aus dem Internet erreichbar ist:

```
SUBLARR_API_KEY=eigener_geheimer_key
```

Alle API-Endpunkte erfordern dann den Header `X-Api-Key: eigener_geheimer_key`.

---

## Unraid Nginx Proxy Manager

1. Einen neuen Proxy Host hinzufügen
2. Domain: `sublarr.eigene-domain.com`
3. Scheme: `http`, Forward Hostname: Containername oder IP, Port: `5765`
4. Checkbox **Websockets Support** aktivieren
5. SSL-Zertifikat hinzufügen (Let's Encrypt)

---

## Häufige Probleme

### Echtzeit-Updates funktionieren nicht

Die WebSocket-Verbindung zu `/socket.io/` fehlt. Prüfen:
- Nginx leitet `Upgrade`- und `Connection`-Header weiter
- Traefik hat die WebSocket-Middleware angewendet
- Proxy-Read-Timeout ist mindestens 300 Sekunden

### 413 Request Entity Too Large

```nginx
client_max_body_size 100M;
```

### 502 Bad Gateway nach Aufwachen aus Standby

Sublarr verwendet einen einzelnen Gunicorn-Worker. Den Proxy-Read-Timeout auf 30 s erhöhen, um falsche 502-Fehler zu vermeiden.
