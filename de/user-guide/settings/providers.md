---
title: Settings — Provider
description: Konfiguration der Untertitel-Provider, unterstützte Provider und Scoring-Algorithmus
published: true
date: 2026-04-10
---

# Untertitel-Provider-System

Dieses Dokument beschreibt die Funktionsweise des Sublarr-Provider-Systems und das Hinzufügen neuer Provider.

## Inhaltsverzeichnis

- [Überblick](#überblick)
- [Vorhandene Provider](#vorhandene-provider)
- [Provider-Architektur](#provider-architektur)
- [Scoring-System](#scoring-system)
- [Neuen Provider hinzufügen](#neuen-provider-hinzufügen)
- [Provider-Best-Practices](#provider-best-practices)
- [Provider testen](#provider-testen)

## Überblick

Sublarr verwendet ein modulares Provider-System, um Untertitel aus mehreren Quellen zu suchen und herunterzuladen. Jeder Provider ist ein eigenständiges Modul, das eine Standard-Schnittstelle implementiert, sodass das System alle Provider einheitlich behandeln kann und gleichzeitig deren individuelle APIs und Features unterstützt.

**Kernfeatures**
- Mehrere Provider werden parallel durchsucht
- Prioritätsbasierte Reihenfolge (konfigurierbar)
- Automatischer Fallback bei Fehlern
- Ergebnis-Scoring und Ranking
- Provider-Health-Monitoring
- HTTP-Session-Management mit Retry-Logik
- Cache-System für Suchergebnisse

## Vorhandene Provider

Sublarr enthält 22 eingebaute Provider. *(Aktualisiert v0.47.3-beta)*

### 1. AnimeTosho

**Am besten für**: Fansub-ASS-Untertitel von Release-Gruppen

**Eigenschaften**
- Keine Authentifizierung erforderlich
- Spezialisiert auf Anime-Untertitel
- Hochwertige ASS-Dateien von Fansubs
- Extrahiert Untertitel aus vollständigen Release-Torrents
- XZ-komprimierte Untertitelarchive
- Feed API (JSON-Format)
- Nutzt AniDB-Episoden-IDs zur Zuordnung

**Konfiguration**
```env
# Kein API-Key nötig
SUBLARR_PROVIDER_PRIORITIES=animetosho,jimaku,opensubtitles,subdl
```

### 2. Jimaku

**Am besten für**: Anime-Untertitel mit AniList-Integration

**Konfiguration**
```env
SUBLARR_JIMAKU_API_KEY=eigener_api_key
```

API-Key unter https://jimaku.cc → Account Settings → Generate API token beziehen.

### 3. OpenSubtitles

**Am besten für**: Breite Abdeckung aller Medientypen

**Konfiguration**
```env
SUBLARR_OPENSUBTITLES_API_KEY=eigener_api_key
SUBLARR_OPENSUBTITLES_USERNAME=eigener_benutzername
SUBLARR_OPENSUBTITLES_PASSWORD=eigenes_passwort
```

API-Key unter https://www.opensubtitles.com/en/consumers beziehen (Genehmigung dauert 1–2 Tage).

### 4. SubDL

**Am besten für**: Subscene-Nachfolger mit breiter Abdeckung

**Konfiguration**
```env
SUBLARR_SUBDL_API_KEY=eigener_api_key
```

API-Key unter https://subdl.com → Settings → API beziehen.

### 5. Gestdown

TV-Serien-Untertitel über Addic7ed-Proxy. Kein API-Key nötig.

### 6. Podnapisi

Mehrsprachige Untertitel mit großer Datenbank. Kein API-Key nötig.

### 7. Kitsunekko

Japanische Anime-Untertitel. Kein API-Key nötig.

### 8. Napisy24

Polnische Untertitel mit Datei-Hash-Matching. Kein API-Key nötig.

### 9. Titrari

Rumänische Untertitel. Kein API-Key nötig.

### 10. LegendasDivx

Portugiesische Untertitel. Login erforderlich — Konfiguration über die Settings-UI.

### 11. Whisper-Subgen (Veraltet)

Veraltet seit v0.9.0-beta. Stattdessen Settings → Whisper verwenden.

---

*Die folgenden Provider wurden in v0.33.0-beta hinzugefügt:*

### 12. Subf2m

**Am besten für**: Breite mehrsprachige Abdeckung (60+ Sprachen)

Kein API-Key nötig. Scrapt Subf2m.co.

### 13. Subsource

**Am besten für**: Mehrsprachige Untertitel für Filme und TV

Kein API-Key nötig.

### 14. YIFY Subtitles

**Am besten für**: Film-Untertitel (nur Filme — keine TV-Serien-Unterstützung)

Nutzt IMDB-IDs über die YIFY JSON API. Kein API-Key nötig.

### 15. Zimuku

**Am besten für**: Chinesische Untertitel (vereinfacht & traditionell)

Kein API-Key nötig. Scrapt Zimuku.net.

### 16. BetaSeries

**Am besten für**: Französische Untertitel für TV-Serien

**Konfiguration**
```env
SUBLARR_BETASERIES_API_KEY=eigener_api_key
```

API-Key unter https://www.betaseries.com/api/ beziehen.

### 17. Titlovi

**Am besten für**: Balkan-Sprachen (Kroatisch, Serbisch, Bosnisch, Slowenisch, Mazedonisch)

Kein API-Key nötig.

### 18. EmbeddedSubtitles *(v0.33.0-beta)*

**Am besten für**: Extraktion bereits in Mediendateien eingebetteter Untertitel

Liest eingebettete Untertitelspuren direkt aus den Videodateien der Bibliothek. Ergebnisse werden wie reguläre Suchergebnisse behandelt und neben externen Providern bewertet/gerankt. Keine externe API nötig — erfordert `ffprobe` im Container (standardmäßig enthalten).

---

*Die folgenden Provider wurden nach v0.33.0-beta hinzugefügt:*

### 19–21. (Weitere Provider)

*(Dokumentation in Bearbeitung)*

### 22. SubsDump

**Typ:** Anime-fokussierte Untertitel-Datenbank
**API-Key erforderlich:** Nein
**Sprachen:** Japanisch → Deutsch/Englisch (Anime-spezialisiert)
**Hinweise:** Selbstgehostete Untertitel-Datenbank für Anime-Inhalte. Erfordert eine lokale SubsDump-Instanz.

## Anti-Captcha

Einige Scraper-basierte Provider (hauptsächlich Kitsunekko und Gestdown unter hoher Last) können CAPTCHA-Challenges ausliefern, die automatische Suchen blockieren. Sublarr unterstützt die Weiterleitung über einen externen Lösungsdienst.

| Einstellung | Umgebungsvariable | Beschreibung |
|-------------|-------------------|--------------|
| Provider | `SUBLARR_ANTI_CAPTCHA_PROVIDER` | Zu verwendender Dienst: `2captcha`, `anticaptcha` oder `capsolver`. Leer lassen zum Deaktivieren. |
| API-Key | `SUBLARR_ANTI_CAPTCHA_API_KEY` | API-Key für den gewählten Dienst. Nur erforderlich, wenn ein Provider gesetzt ist. |

> Anti-Captcha wird nur ausgelöst, wenn ein Provider eine CAPTCHA-Challenge zurückgibt — normale Anfragen sind nicht betroffen. Für die meisten Homelab-Setups ist es nicht erforderlich.

---

## Plugin-Entwicklung

Seit v0.9.0-beta unterstützt Sublarr das Laden eigener Untertitel-Provider als Plugins. Plugin-Dateien in `/config/plugins/` ablegen. Siehe [Plugin-Entwicklung](/development/plugin-development) für die vollständige Dokumentation.

---

## Untertitel-Scoring

Sublarr bewertet jedes Suchergebnis vor dem Download. Höhere Scores gewinnen.

### Basis-Score nach Format

| Format | Basis-Score |
|---|---|
| ASS | 300 |
| SSA | 280 |
| SRT | 150 |
| VTT | 100 |
| Andere | 50 |

ASS wird am höchsten bewertet, da es Fansub-Styling (Schriftarten, Farben, Typesetting) bewahrt, das SRT nicht darstellen kann.

### Dateigrößen-Bonus

| Dateigröße | Bonus |
|---|---|
| > 200 KB | +40 |
| > 100 KB | +30 |
| > 50 KB | +20 |
| ≤ 50 KB | +0 |

### Hash-Match-Bonus

| Match | Bonus |
|---|---|
| Exakter Hash-Match | +100 |
| Kein Hash-Match | +0 |

### Metadaten-Boni

| Signal | Bonus |
|---|---|
| Serienname-Match | +30 |
| Jahres-Match | +20 |
| Staffel-Match | +15 |
| Episoden-Match | +15 |
| Release-Group-Match | +20 |
| ASS-Format (Sprachprofil-Präferenz) | +50 |

### Uploader-Vertrauens-Bonus

| Uploader-Rang | Bonus |
|---|---|
| Gold/Diamond | +20 |
| Silver | +15 |
| Bronze | +10 |
| Ohne Rang | +0 |

### Maschinelle-Übersetzung-Malus

| Flag | Malus |
|---|---|
| `mt`- oder `ai`-Tag | -50 |

### Pro-Provider-Score-Modifikator

Jeder Provider kann einen manuellen Score-Modifikator (-100 bis +100) in den Settings erhalten. Positive Modifikatoren für vertrauenswürdige Provider, negative für historisch niedrige Qualität verwenden.

### Upgrade-Scoring

**Formatregeln**

1. **Niemals downgraden** — ASS → SRT ist unabhängig vom Score blockiert
2. **Format immer upgraden** — SRT → ASS wird immer ausgeführt, wenn `upgrade_prefer_ass = true`
3. **Gleiches Format** — nur upgraden, wenn das Score-Delta `upgrade_min_score_delta` (Standard: 50) überschreitet

**Aktualitätsschutz:** Untertitel, die innerhalb von `upgrade_window_days` (Standard: 7 Tage) heruntergeladen wurden, erfordern das **2-fache des Mindest-Score-Deltas**.

### Fansub-Gruppen-Regeln (v0.24.3+)

| Regel | Auswirkung |
|-------|------------|
| **Bevorzugte Gruppe** erkannt | +`bonus` Punkte (Standard: +20) |
| **Ausgeschlossene Gruppe** erkannt | −999 Punkte (effektiv ausgeschlossen) |

Globale Release-Group-Einstellungen werden unter **Settings → Scoring → Release Group Filter** konfiguriert. Pro-Serien-Überschreibungen werden über die **Fansub-Schaltfläche** in der Seriendetail-Toolbar gesetzt (v0.33.0+, ersetzt die alte Inline-Karte).

### Endgültige Score-Formel

```
score = format_base
      + size_bonus
      + hash_match_bonus
      + series_bonus + year_bonus + season_bonus + episode_bonus
      + release_group_bonus
      + format_preference_bonus
      + uploader_trust_bonus
      - machine_translation_penalty
      + provider_modifier
      + fansub_preference_bonus
```

Das Ergebnis mit dem höchsten Score oberhalb des Mindestschwellenwerts wird heruntergeladen.
