---
title: Settings — Prompt Templates
description: Customize the system and user prompts sent to the LLM for subtitle translation
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

# Settings — Prompt Templates

![Settings — Prompt Templates](/img/screen-settings-prompt-templates.png)

Prompt templates control exactly what Sublarr sends to the LLM when translating subtitles. The **system prompt** defines the LLM's persona and behavioral constraints; the **user prompt** is the per-subtitle instruction that includes the actual text to translate.

> **Warning:** Poor prompt templates significantly degrade translation quality. Always test changes against a small subtitle file — ideally a known episode with existing good subtitles for comparison — before enabling a modified template globally.

---

## Default Templates

Sublarr ships with an optimized default prompt pair tuned for anime subtitle translation. The defaults are based on extensive testing with both general-purpose models (GPT-4o, Claude) and the Sublarr custom fine-tuned model and work well out of the box.

**Changing the default templates is for advanced users only.** If you are using the Sublarr fine-tuned Ollama model, the default templates are specifically calibrated for it — modifying them may reduce output quality.

For most users, per-series AI Glossary entries (configured in **Library → Series → AI Glossary**) are sufficient to customize translation behavior without touching the prompts.

---

## Template Variables

Both the system prompt and user prompt support the following variables. All variables are substituted at translation time before the prompt is sent to the LLM.

| Variable | Description |
|----------|-------------|
| `{{ source_language }}` | Full name of the source language (e.g. `Japanese`) |
| `{{ target_language }}` | Full name of the target language (e.g. `German`) |
| `{{ glossary }}` | Series-specific AI Glossary entries as a formatted list. Empty string if no glossary entries are defined for this series. |
| `{{ subtitle_block }}` | The subtitle text to be translated. For ASS files, this is the dialogue cue content stripped of formatting tags. |
| `{{ series_title }}` | The name of the series the subtitle belongs to (e.g. `Fullmetal Alchemist: Brotherhood`) |

### Using the Glossary Variable

When a series has AI Glossary entries defined, `{{ glossary }}` expands to a block like:

```
Glossary:
- Titan → Titan (keep untranslated)
- Survey Corps → Aufklärungstruppe
- ODM gear → ODM-Ausrüstung
```

If no glossary exists for the series, the variable is an empty string. Use a conditional to avoid empty sections in your prompt:

```jinja2
{% if glossary %}
Use this glossary for character names and series-specific terms:
{{ glossary }}
{% endif %}
```

---

## System Prompt

The system prompt is sent as the `system` role message (or equivalent) to the LLM. It defines the model's persona, capabilities, and hard constraints for the translation task.

**Field:** Multi-line textarea, no length limit (but excessively long system prompts reduce effective context for the subtitle content).

The default system prompt establishes the LLM as a professional subtitle translator with knowledge of anime conventions, instructs it to preserve timing markers and ASS formatting tags, and sets expectations around honorifics, onomatopoeia, and untranslatable cultural terms.

---

## User Prompt

The user prompt is sent as the `user` role message for each subtitle batch. It contains the actual translation instruction and embeds the subtitle text via `{{ subtitle_block }}`.

**Field:** Multi-line textarea.

The default user prompt follows this pattern:

```jinja2
Translate the following {{ source_language }} subtitle dialogue to {{ target_language }}.
Series: {{ series_title }}

{% if glossary %}
Terminology:
{{ glossary }}
{% endif %}

Rules:
- Preserve all ASS formatting tags exactly ({\an8}, {\i1}, etc.)
- Do not translate proper nouns unless listed in the glossary
- Match the register and tone of the original line
- Output only the translated dialogue, in the same line order as the input

Dialogue:
{{ subtitle_block }}
```

---

## Per-Language Overrides

In addition to the global default templates, each translation profile can define its own prompt overrides. This allows different prompts for different language pairs — for example, a stricter prompt for Japanese → German that enforces specific grammatical conventions, while using a more relaxed prompt for Japanese → English.

Per-language overrides are configured in **Settings → Profiles → [Profile name] → Advanced → Prompt Override**.

When a per-profile override is set, it takes precedence over the global default. The **Reset to Defaults** button on the profile page removes the override and falls back to the global template.

---

## Resetting to Defaults

| Button | Action |
|--------|--------|
| **Reset System Prompt** | Restores only the system prompt to the built-in default |
| **Reset User Prompt** | Restores only the user prompt to the built-in default |
| **Reset to Defaults** | Restores both prompts at once |

Resetting the global defaults does not affect per-profile prompt overrides — those must be reset individually from the profile page.
