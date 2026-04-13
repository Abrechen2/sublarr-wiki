---
title: FAQ
description: Häufig gestellte Fragen zu Sublarr
published: true
date: 2026-03-14
---

# Häufig gestellte Fragen

---

## Allgemein

### Was ist Sublarr?

Sublarr ist ein selbstgehosteter Untertitel-Manager für Anime- und Medienbibliotheken. Das Programm durchsucht Untertitel-Provider, bewertet und lädt den besten Treffer herunter (ASS-bevorzugt für Anime) und übersetzt Untertitel mit einem lokalen LLM in die Zielsprache — alles ohne Daten an Drittanbieter zu senden.

### Ersetzt Sublarr Bazarr?

Sublarr ist eine Bazarr-Alternative, kein direkter Ersatz. Wesentliche Unterschiede:

- Sublarr ist Anime-fokussiert (ASS-Format bevorzugt, Fansub-Bewertung, AniDB-Integration)
- Sublarr enthält LLM-Übersetzung (Bazarr nicht)
- Sublarr hat weniger Provider als Bazarr, deckt aber die wichtigsten für Anime ab
- Bazarr unterstützt mehr exotische Provider und Sprachen

Für Anime-lastige Bibliotheken mit Fokus auf Übersetzungsqualität ist Sublarr die bessere Wahl.

### Ist Sublarr kostenlos?

Ja. Sublarr ist Open Source unter GPL-3.0. Die einzigen kostenpflichtigen Komponenten sind optionale Drittanbieter-Dienste (z. B. DeepL API, OpenSubtitles VIP, OpenAI API). Ollama selbst (für lokale LLM-Übersetzung) ist ebenfalls kostenlos.

---

## Einrichtung

### Was ist das Minimal-Setup?

1. `SUBLARR_MEDIA_PATH` auf das Medienverzeichnis setzen
2. `SUBLARR_OLLAMA_URL` setzen, falls Ollama nicht auf localhost läuft
3. Mindestens einen Provider-API-Key hinzufügen (oder AnimeTosho verwenden, das keinen Key benötigt)

Das reicht für den Start. Alles andere hat sinnvolle Standardwerte.

### Werden Sonarr oder Radarr benötigt?

Nein. Den Standalone-Modus mit `SUBLARR_STANDALONE_ENABLED=true` aktivieren und Sublarr überwacht das Medienverzeichnis direkt. Die Sonarr-/Radarr-Integration bietet jedoch bessere Episoden-Metadaten und automatische Webhook-Trigger.

### Welches Modell für die Übersetzung?

Für die Übersetzung japanischer Anime-Untertitel ins Englische/Deutsche liefert `qwen2.5:14b-instruct` die beste Qualität. Bei begrenztem VRAM (unter 8 GB) ist `qwen2.5:7b-instruct` die Alternative. Beide übertreffen einfache maschinelle Übersetzung bei Anime-Dialogen deutlich.

### Warum ist die Übersetzung langsam?

LLM-Übersetzung ist CPU-/GPU-gebunden. Einflussfaktoren auf die Geschwindigkeit:

- **Modellgröße**: 14B ist ca. 2× langsamer als 7B
- **Batch-Größe**: Kleinere Batches (`SUBLARR_BATCH_SIZE=10`) sind sicherer, aber langsamer
- **Hardware**: GPU mit ausreichend VRAM ist deutlich schneller als CPU
- **Kontextlänge**: Längere Untertitel benötigen mehr Zeit pro Batch

Für höheren Durchsatz ein 7B-Modell auf GPU verwenden und `SUBLARR_BATCH_SIZE=20` setzen.

---

## Untertitel

### ASS vs. SRT — was verwenden?

Für Anime immer ASS bevorzugen. ASS-Dateien von Fansub-Gruppen enthalten:
- Gestylte Dialoge (Schriftarten, Farben, Positionierung)
- Typesetting für Signs und Overlays
- Karaoke-Effekte

SRT ist reiner Text. Sublarr bewertet ASS +150 Punkte über SRT und wird bei Upgrades niemals von ASS auf SRT downgraden.

### Warum lädt Sublarr keine Untertitel herunter?

Häufige Ursachen:

1. **Kein passender Provider gefunden** — prüfen, welche Provider aktiviert sind und API-Keys besitzen
2. **Provider-Circuit-Breaker OPEN** — ein Provider ist zu oft fehlgeschlagen; Cooldown abwarten oder in den Einstellungen zurücksetzen
3. **Eintrag nicht in der Wanted-Queue** — auf der Wanted-Seite prüfen, ob die Datei gescannt wurde
4. **Path Mapping falsch** — wenn Sonarr und Sublarr unterschiedliche Mount-Pfade nutzen, `SUBLARR_PATH_MAPPING` setzen
5. **Kein Sprachprofil zugewiesen** — die Serie benötigt ein in der Bibliothek zugewiesenes Sprachprofil

Auf den Seiten **Queue** und **Activity** nach Fehlerdetails schauen.

### Warum erscheinen Untertitel nach dem Download nicht in Jellyfin?

Sublarr löst nach jedem Download automatisch eine Bibliotheks-Aktualisierung aus. Falls Untertitel nicht erscheinen:

1. Prüfen, ob der Medienserver unter Settings → Media Servers konfiguriert ist
2. Den API-Key mit der Test-Schaltfläche verifizieren
3. Sicherstellen, dass Sublarr und Jellyfin dieselben Dateipfade sehen (Path Mapping prüfen)
4. In Jellyfin manuell einen Bibliotheksscan anstoßen

### Kann Sublarr vorhandene Untertitel übersetzen?

Ja. Unter **Library → Serie → Episode** die Übersetzen-Schaltfläche bei einem vorhandenen Untertitel klicken. Bei Änderung des Modells oder Prompts kann auch erneut übersetzt werden.

### Welche Untertitelformate werden unterstützt?

- **Download:** ASS, SSA, SRT, VTT (providerabhängig)
- **Übersetzung:** ASS und SRT
- **Konvertierung:** ASS ↔ SRT ↔ SSA ↔ VTT über das Formatkonvertierungs-Tool
- **OCR:** PGS- und VobSub-Bildspuren via Tesseract

---

## Übersetzung

### Welche Sprachen werden unterstützt?

Jedes Sprachpaar, das vom gewählten LLM oder Übersetzungs-Backend unterstützt wird. Ollama-Modelle sind besonders gut bei:
- Japanisch → Englisch
- Japanisch → Deutsch
- Chinesisch → Englisch
- Koreanisch → Englisch

Für europäische Sprachpaare liefern DeepL oder LibreTranslate möglicherweise bessere Ergebnisse als Ollama.

### Kann ein eigenes Übersetzungs-Prompt verwendet werden?

Ja. Unter **Settings → Translation → Prompt Presets** ein eigenes Preset erstellen oder eines der 5 eingebauten Templates nutzen (Anime, Documentary, Casual, Literal, Dubbed). Das Prompt unterstützt die Platzhalter `{source_language}` und `{target_language}`.

### Was ist Translation Memory?

Translation Memory speichert vorherige Übersetzungen. Wenn dieselbe (oder eine sehr ähnliche) Untertitelzeile erneut auftaucht, verwendet Sublarr die gecachte Übersetzung statt das LLM erneut aufzurufen. Das beschleunigt die Neuübersetzung von Serien mit wiederkehrenden Dialogmustern erheblich.

Cache-Abgleich nutzt:
- **SHA-256 Exact Match** — identische Zeilen werden sofort wiederverwendet
- **difflib Similarity** — Zeilen über 90 % Ähnlichkeitsschwelle nutzen die gecachte Übersetzung

---

## Performance

### Sublarr verbraucht beim Scannen viel CPU

Der Wanted-Scanner führt ffprobe auf jeder Videodatei aus, um vorhandene eingebettete Untertitel zu erkennen. Bei großen Bibliotheken kann das CPU-intensiv sein. Abhilfe:

- `SUBLARR_SCAN_METADATA_MAX_WORKERS=2` setzen (Standard ist 4)
- Inkrementelles Scannen aktivieren (nur geänderte Dateien werden erneut gescannt)
- Scan-Intervall erhöhen: `SUBLARR_WANTED_SCAN_INTERVAL_HOURS=12`

### Kann PostgreSQL statt SQLite verwendet werden?

Ja. `SUBLARR_DATABASE_URL=postgresql://user:pass@host:5432/sublarr` setzen. PostgreSQL wird für Bibliotheken mit 1000+ Serien oder gleichzeitigem Zugriff mehrerer Prozesse empfohlen.

---

## Updates & Backup

### Wie wird Sublarr aktualisiert?

```bash
docker pull ghcr.io/abrechen2/sublarr:latest
docker compose up -d
```

Vor dem Update das CHANGELOG.md auf Breaking Changes prüfen.

### Wie wird die Konfiguration gesichert?

**Settings → System → Backup** erstellt ein ZIP mit Datenbank und Konfiguration. Automatische Backups laufen nach konfigurierbarem Zeitplan. Backup-Dateien werden unter `SUBLARR_BACKUP_DIR` (Standard: `/config/backups`) gespeichert.

### Wie wird ein Backup wiederhergestellt?

**Settings → System → Restore** → die Backup-ZIP-Datei hochladen. Das ersetzt die aktuelle Datenbank und Konfiguration. Sublarr startet nach der Wiederherstellung automatisch neu.
