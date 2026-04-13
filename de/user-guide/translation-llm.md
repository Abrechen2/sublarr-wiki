---
title: Übersetzung & LLM
description: Lokale LLM-Übersetzung mit Ollama — eigenes Anime-Modell, Übersetzungs-Pipeline
published: true
date: 2026-03-14
---

# Übersetzung & LLM

Sublarr unterstützt vollständig offline Untertitelübersetzung mit [Ollama](https://ollama.com). Keine Cloud-APIs, keine Konten erforderlich.

## Eigenes Anime-Modell

Sublarr liefert ein feinjustiertes Anime-Übersetzungsmodell, das auf 75.000 Untertitelpaaren trainiert wurde:

```bash
ollama pull hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

| Eigenschaft | Wert |
|-------------|------|
| Richtung | Englisch → Deutsch |
| Trainingsdaten | OPUS OpenSubtitles v2018, 75k Anime-Untertitelpaare |
| BLEU-1 | 0,281 |
| Größe | 7 GB (Q4_K_M GGUF) |
| Konfigurationsschlüssel | `SUBLARR_OLLAMA_MODEL` |

## Ollama konfigurieren

In `.env` oder Settings → Translation setzen:

```env
SUBLARR_OLLAMA_URL=http://ollama:11434
SUBLARR_OLLAMA_MODEL=hf.co/Sublarr/anime-translator-v6-GGUF:Q4_K_M
```

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

## Chat API & Serienkontext (V9+)

Siehe [Settings — Übersetzung: Chat API](/user-guide/settings/translation#ollama-chat-api-v9)
für die vollständige Referenz einschließlich der Konfiguration von System-Prompts und
Serienkontext-Injection.
