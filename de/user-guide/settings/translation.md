---
title: Settings — Übersetzung
description: LLM-Übersetzungs-Backend-Konfiguration — Ollama, eigenes Modell
published: true
date: 2026-03-14
---

# Settings — Übersetzung

> **Beta-Feature**
> Die KI-Übersetzungsfunktion ist experimentell und noch nicht zuverlässig genug für den Produktiveinsatz. Die Ergebnisse variieren stark je nach Modell, Prompt und Eingabequalität. Nutzung auf eigene Verantwortung.

### Übersetzungs-Backends

Sublarr unterstützt mehrere Übersetzungs-Backends. Konfiguration unter Settings > Translation Backends.

| Backend | Typ | Selbstgehostet | API-Key | Am besten für |
|---------|-----|----------------|---------|---------------|
| **Ollama** | LLM | Ja | Nein | Volle Kontrolle, eigene Prompts, GPU-beschleunigt |
| **DeepL** | API | Nein | Ja | Hochwertige europäische Sprachen |
| **LibreTranslate** | API | Ja | Optional | Selbstgehostet, datenschutzorientiert |
| **OpenAI-kompatibel** | LLM | Beides | Ja | GPT-4, lokale LLMs mit OpenAI-API |
| **Google Cloud** | API | Nein | Ja | Breite Sprachunterstützung, schnell |

**Ollama konfigurieren (Standard):**
1. Ollama auf dem Server installieren
2. Ein Modell pullen: `ollama pull qwen2.5:14b-instruct`
3. In Sublarr: Settings > Translation Backends > Ollama
4. Ollama-URL und Modellname eingeben
5. Test klicken zur Überprüfung

**Fallback-Ketten:**
Backup-Backends für den Ausfall des Primär-Backends konfigurieren. Beispiel:
1. Primär: Ollama (lokal, schnell, kostenlos)
2. Fallback 1: DeepL (Cloud, hohe Qualität)
3. Fallback 2: LibreTranslate (selbstgehostetes Backup)

## Ollama — Chat API (V9+)

Sublarr v0.38.0 hat die Unterstützung für den Ollama-Endpunkt `/api/chat` zusätzlich zum
bestehenden Endpunkt `/api/generate` hinzugefügt.

### Chat API aktivieren

Unter **Settings → Translation Backends → Ollama** die Checkbox **Chat API (V9+)** aktivieren.

| Einstellung | Standard | Beschreibung |
|---|---|---|
| Chat API (V9+) | Aus | `/api/chat` statt `/api/generate` verwenden |
| System Prompt | _(siehe unten)_ | Systemnachricht als erster Chat-Turn eingefügt |

### Chat vs. Generate — Unterschiede

| | `/api/generate` (Standard) | `/api/chat` (V9+) |
|---|---|---|
| Request-Format | `{"prompt": "..."}` | `{"messages": [{"role": "system", ...}, {"role": "user", ...}]}` |
| System-Prompt | Im User-Prompt eingebettet | Separater `system`-Message |
| Serienkontext | Nicht unterstützt | Injiziert via `{series_context}`-Platzhalter |
| Unterstützte Modelle | Alle Ollama-Modelle | Modelle mit Instruction-Following (Qwen2.5, Llama 3+) |

### System-Prompt & Serienkontext

Der System-Prompt ist die erste Nachricht, die im Chat-Modus an das Modell gesendet wird.
Der Standard-System-Prompt weist das Modell an, Anime-Untertitel ins Deutsche zu übersetzen
und dabei eine informelle Sprache (`du`-Form) zu verwenden.

Der Platzhalter `{series_context}` kann im System-Prompt eingefügt werden.
Sublarr ersetzt ihn zum Übersetzungszeitpunkt durch den Seriennamen und das Genre. Das
verbessert die Konsistenz bei Charakternamen und Terminologie über eine Episode hinweg.

**Beispiel-System-Prompt mit Serienkontext:**
```
Du bist ein Anime-Untertitel-Übersetzer EN→DE. {series_context}
Übersetze präzise und natürlich. Keine Erklärungen — nur die Übersetzung.
```

Wenn Serienkontext verfügbar ist (z. B. „Serie: Naruto. Genre: Action."), wird
der Platzhalter ersetzt. Ohne verfügbaren Kontext wird der Platzhalter sauber
entfernt.

### Welche Modelle profitieren von der Chat API?

| Modell | Empfehlung |
|---|---|
| `qwen2.5:14b-instruct` | Chat API empfohlen — besseres Instruction Following |
| `llama3.2:3b` | Chat API empfohlen |
| Custom `anime-translator-v8-GGUF` | Generate API — auf Generate-Format-Prompts feinjustiert |
| Custom `anime-translator-v9-GGUF` | Chat API — mit Chat-Format-Prompts trainiert |
| DeepSeek-R1 | Chat API empfohlen |

> **Faustregel:** Endet der Modellname auf `-instruct`, die Chat API verwenden.
> Bei Verwendung des eigenen Sublarr-Modells die Modellversion prüfen:
> V8 und früher → Generate, V9+ → Chat.
