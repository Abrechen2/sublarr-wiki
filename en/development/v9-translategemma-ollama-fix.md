---
title: V9 TranslateGemma — Ollama Deployment Fix
description: Required Modelfile fix for TranslateGemma-based models in Ollama (V9+)
published: true
date: 2026-03-17
---

# V9 TranslateGemma — Ollama Deployment Fix

## Problem

`google/translategemma-12b-it` verwendet ein **eigenes Jinja2 Chat-Template**, das den
`content`-Parameter als **Liste** mit `source_lang_code`/`target_lang_code`-Feldern erwartet:

```python
# Was das Template erwartet (Python/HuggingFace):
messages = [{"role": "user", "content": [
    {"type": "text", "source_lang_code": "en", "target_lang_code": "de", "text": "..."}
]}]
```

**Ollama** unterstützt dieses List-Format nicht — es übergibt `content` immer als String.
Das GGUF-Modell enthielt das originale TranslateGemma-Template. Beim `ollama create` mit
einem einfachen `SYSTEM`-Prompt wurde das Template aufgerufen, konnte aber kein valides
`source_lang_code` extrahieren → **Halluzination auf Englisch** statt Übersetzung.

### Symptom

Ohne Fix übersetzt V9 **nicht** — es generiert stattdessen englische Fortsetzungen:
```
EN:  I'm home!
V9:  I've been gone for a while, but now I'm back. It was nice to be away...
```

---

## Fix: Modelfile mit Passthrough-Template

Das Modelfile muss das eingebettete TranslateGemma-Template **überschreiben** mit dem
gleichen simplen Passthrough-Template wie `translategemma:latest` auf Ollama Hub:

```
FROM /Users/denniswittke/models/anime-translator-en-de-v9-Q4_K_M.gguf

TEMPLATE """{{- range $i, $_ := .Messages }}
{{- $last := eq (len (slice $.Messages $i)) 1 }}
{{- if or (eq .Role "user") (eq .Role "system") }}<start_of_turn>user
{{ .Content }}<end_of_turn>
{{ if $last }}<start_of_turn>model
{{ end }}
{{- else if eq .Role "assistant" }}<start_of_turn>model
{{ .Content }}{{ if not $last }}<end_of_turn>
{{ end }}
{{- end }}
{{- end }}"""

PARAMETER stop <end_of_turn>
PARAMETER temperature 0.3
PARAMETER top_p 0.9
PARAMETER top_k 40
PARAMETER repeat_penalty 1.1
PARAMETER num_predict 200
```

**Kein `SYSTEM`-Block im Modelfile** — der Translator-Prompt kommt vom Aufrufer.

---

## Prompt-Format für Inference

Da das Template ein Passthrough ist, muss der **volle Translator-Prefix** im User-Content
mitgeliefert werden. Für direkte Ollama-Aufrufe (z.B. `06_test_translation.py`, `10_evaluate.py`):

```python
TRANSLATE_PROMPT = (
    "You are a professional English (en) to German (de) translator. "
    "Your goal is to accurately convey the meaning and nuances of the original English text "
    "while adhering to German grammar, vocabulary, and cultural sensitivities.\n"
    "Produce only the German translation, without any additional explanations or commentary. "
    "Please translate the following English text into German:\n\n\n"
    "{text}"
)
```

Für **Sublarr** (System+User via `client.chat()`): Das Passthrough-Template wandelt die
System-Message ebenfalls in einen `<start_of_turn>user`-Block um — V9 verarbeitet das korrekt.
Der Sublarr-Default-Prompt funktioniert **ohne weitere Anpassung**.

---

## Betroffene Dateien

| Datei | Änderung |
|-------|---------|
| `lang_config.py` — `EN_DE_V9.translate_prompt` | Voller Translator-Prefix statt `{text}` |
| `eval_config.py` — `EVAL_MODEL_PROMPTS` | Pro-Modell-Prompt-Map ergänzt |
| `10_evaluate.py` | Nutzt `EVAL_MODEL_PROMPTS`; BERTScore try/except |
| Ollama Modelfile auf Mac mini | Passthrough-Template statt TranslateGemma-Template |

---

## Für zukünftige Versionen (V10+)

Beim Export eines TranslateGemma-basierten Modells via `05_export_gguf.py` gilt:
- Das GGUF enthält das originale Template eingebettet
- **Immer** ein Modelfile mit Passthrough-Template beim `ollama create` verwenden
- Das Modelfile in `/root/anime-translator-en-de-vX-gguf/Modelfile-...` wird von `05_export_gguf.py` erstellt — sicherstellen dass es das korrekte Template hat (nicht nur `SYSTEM`)
- In `05_export_gguf.py` die `deploy_to_mac()`-Funktion bereits mit dem korrekten Template aktualisiert (Stand V9)
