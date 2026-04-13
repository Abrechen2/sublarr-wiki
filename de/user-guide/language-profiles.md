---
title: Sprachprofile
description: Sprachzuweisung pro Serie — Quellsprache, Zielsprache, Scoring-Schwellenwerte
published: true
date: 2026-03-14
---

# Sprachprofile

Sprachprofile steuern, nach welchen Untertitelsprachen Sublarr sucht und in welche Sprache übersetzt wird — individuell pro Serie.

---

## Überblick

Ein Sprachprofil ist eine benannte Konfiguration mit folgenden Angaben:

- **Zielsprachen** — welche Sprachen gesucht oder als Übersetzungsziel verwendet werden
- **Formatpräferenz** — ASS bevorzugt, SRT als Fallback oder beliebig
- **Übersetzungseinstellungen** — welches Backend und Prompt-Preset verwendet werden
- **Upgrade-Regeln** — wann ein vorhandener Untertitel ersetzt wird

Jeder Serie oder jedem Film kann ein Sprachprofil zugewiesen werden. Ohne Zuweisung gelten die globalen Standardwerte aus den Einstellungen.

---

## Sprachprofil erstellen

1. Zu **Settings → Translation → Language Profiles** navigieren
2. **Add Profile** klicken
3. Konfigurieren:
   - **Name** — z. B. „Anime DE", „Filme EN", „Dokumentation FR"
   - **Target Language** — Zielsprache der Übersetzung (oder des Downloads)
   - **Source Language** — Originalsprache der Untertitel (üblicherweise `en`)
   - **Format Preference** — ASS bevorzugt, nur SRT oder beliebig
   - **Translation Backend** — Ollama, DeepL, LibreTranslate usw.
   - **Prompt Preset** — welcher Übersetzungsstil angewendet wird
4. Speichern

---

## Profile Serien zuweisen

### Manuelle Zuweisung

1. Zu **Library → Serie** navigieren
2. Eine Serie anklicken
3. In der Seriendetailansicht das Sprachprofil aus dem Dropdown auswählen
4. Speichern

### Tag-basierte automatische Zuweisung

Bei Verwendung von Sonarr-/Radarr-Tags kann Sublarr Profile automatisch anhand von Tags zuweisen:

1. Zu **Settings → Integrations → Tag Profile Mapping** navigieren
2. Zuordnungen hinzufügen wie:
   - Tag `anime-de` → Profil „Anime DE"
   - Tag `documentary` → Profil „Dokumentation FR"
3. Speichern

Sublarr liest Tags aus Sonarr/Radarr bei der Webhook-Verarbeitung und weist das passende Profil automatisch zu.

---

## Mehrsprachige Profile

Ein Profil kann mehrere Sprachen gleichzeitig als Ziel haben. Beispiel: Ein Profil, das sowohl deutsche als auch französische Untertitel herunterlädt:

1. Im Profil-Editor mehrere Zielsprachen hinzufügen
2. Sublarr sucht und übersetzt für jede Sprache unabhängig
3. Pro-Sprach-Einstellungen (Backend, Prompt) können pro Eintrag überschrieben werden

---

## Standardprofil

Das **Globale Standard**-Profil greift, wenn kein serienspezifisches Profil zugewiesen ist. Die Standardwerte unter **Settings → Translation** festlegen:

```
SUBLARR_SOURCE_LANGUAGE=en
SUBLARR_TARGET_LANGUAGE=de
```

---

## Profil-Lookup-Reihenfolge

Bei der Verarbeitung einer Untertitelanfrage prüft Sublarr Profile in dieser Reihenfolge:

1. Serienspezifisches Profil (in der Bibliothek gesetzt)
2. Tag-basiert zugewiesenes Profil (aus Sonarr-/Radarr-Tags)
3. Globale Standardeinstellungen

---

## Format-Scoring mit Profilen

Profile interagieren mit dem Scoring-System:

- **ASS bevorzugt** — ASS-Kandidaten erhalten +50 Bonus im Scoring
- **Nur SRT** — ASS-Kandidaten werden mit 0 bewertet (effektiv herausgefiltert)
- **Upgrade Prefer ASS** — SRT → ASS Upgrade wird unabhängig vom Score-Delta ausgelöst

Für Anime immer **ASS bevorzugt** mit aktiviertem **Upgrade Prefer ASS** verwenden. Das stellt sicher, dass gestylte Fansub-Untertitel bevorzugt werden, wenn verfügbar.

---

## Übersetzungs-Pipeline pro Profil

Die Übersetzungs-Pipeline für jedes Profil:

1. **Suche** — alle aktivierten Provider nach der Zielsprache abfragen
2. **Scoring** — Ergebnisse nach Format, Dateigröße, Hash-Match, Uploader-Vertrauen bewerten
3. **Download** — den besten Treffer herunterladen
4. **HI-Entfernung** (falls aktiviert) — Hörgeschädigten-Markierungen entfernen
5. **Übersetzung** — das konfigurierte Backend mit dem konfigurierten Prompt aufrufen
6. **Qualitäts-Score** (falls aktiviert) — LLM bewertet jede übersetzte Zeile; niedrige Scores lösen einen Retry aus
7. **Speichern** — `.{lang}.ass` oder `.{lang}.srt` neben der Videodatei schreiben
8. **Medienserver-Refresh** — Bibliotheksscan auf konfigurierten Servern auslösen

---

## Fehlerbehebung

**Profil wird nicht auf Serie angewendet:**
- Settings → Integrations → Tag Profile Mapping auf Konflikte prüfen
- Sicherstellen, dass die Serie in der Bibliothek ist (Sublarr muss sie zuerst gescannt haben)

**Falsche Sprache heruntergeladen:**
- Den Zielsprach-Code auf Korrektheit prüfen (ISO 639-1: `en`, `de`, `ja`, `fr` usw.)
- Prüfen, ob der Provider die Zielsprache unterstützt

**Übersetzung startet nicht:**
- Sicherstellen, dass `SUBLARR_WEBHOOK_AUTO_TRANSLATE=true` gesetzt ist
- Die Queue-Seite auf fehlgeschlagene Übersetzungsjobs prüfen
