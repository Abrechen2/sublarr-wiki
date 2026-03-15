---
title: Settings — Translation Backends
description: Configure LLM backends for subtitle translation — Ollama, OpenAI, Anthropic, or a custom HTTP endpoint
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

# Settings — Translation Backends

![Settings — Translation Backends](/img/screen-settings-translation-backends.png)

Translation backends are the LLM services that Sublarr calls when translating subtitle files. Multiple backends can be configured and saved simultaneously — each translation profile specifies which backend to use. This lets you, for example, use a local Ollama model for anime and a cloud API for general content.

> **Tip:** For anime translation, the Sublarr custom fine-tuned model running via Ollama provides the best results for Japanese → German and Japanese → English. The model is optimized for subtitle-length dialogue, preserves ASS formatting tags, and handles honorifics and cultural terms correctly. See [Translation & LLM](/user-guide/translation-llm) for installation instructions.

---

## Supported Backends

| Backend | Type | Notes |
|---------|------|-------|
| **Ollama** | Local | Default backend. Connects to any locally running Ollama server. Works with any GGUF model loaded in Ollama, including the Sublarr fine-tuned anime model. No API key required. |
| **OpenAI API** | Cloud | Compatible with OpenAI, Azure OpenAI, and any OpenAI-compatible endpoint (LM Studio, vLLM, llama.cpp server, Groq, etc.). Requires an API key. |
| **Anthropic** | Cloud | Uses Claude models via the Anthropic Messages API. Requires an Anthropic API key. |
| **Custom HTTP** | API | POST to any HTTP endpoint. You define the request template and response extraction path. Useful for self-hosted inference servers with non-standard APIs. |

---

## Ollama Configuration

Ollama runs locally and requires no API key. The default host assumes Ollama is running on the same machine or Docker host as Sublarr.

| Setting | Default | Description |
|---------|---------|-------------|
| **Host URL** | `http://localhost:11434` | Base URL of the Ollama API. For Docker networks, use the container name or host gateway (e.g. `http://host.docker.internal:11434` on Docker Desktop, or `http://192.168.178.155:11434` for a remote Ollama server). |
| **Model name** | *(required)* | The exact model name as it appears in `ollama list` (e.g. `sublarr-anime-translator-v7`, `llama3.2`, `mistral`). |
| **Timeout** | `120` seconds | Maximum wait for a single generation response. Increase for large subtitle batches or slow hardware. |
| **Max retries** | `3` | Number of retry attempts if the Ollama call fails or times out. |

**Docker networking note:** If Sublarr runs in a container and Ollama runs on the host, `localhost` inside the container resolves to the container itself — not the host. Use `host.docker.internal` (Docker Desktop) or the host's LAN IP.

---

## OpenAI Configuration

This backend is compatible with the OpenAI Chat Completions API format. It works with the official OpenAI service and any third-party service that implements the same interface.

| Setting | Default | Description |
|---------|---------|-------------|
| **API Base URL** | `https://api.openai.com/v1` | Change this to use Azure OpenAI, LM Studio (`http://localhost:1234/v1`), Groq (`https://api.groq.com/openai/v1`), or any other compatible endpoint. |
| **API Key** | *(required)* | Your OpenAI API key (starts with `sk-`). For Azure OpenAI, use the deployment key from Azure Portal. For OpenAI-compatible local servers, this can often be any non-empty string. |
| **Model** | `gpt-4o` | Model identifier as accepted by the API (e.g. `gpt-4o`, `gpt-4o-mini`, `gpt-3.5-turbo`). For Azure OpenAI, this is the deployment name. |
| **Max tokens** | `2000` | Maximum tokens in the completion response. For subtitle batches of 10–15 lines, 2000 is sufficient. Increase if you send larger batches. |
| **Temperature** | `0.3` | Controls output randomness. Lower values (0.1–0.3) produce more consistent, deterministic translations. Higher values (0.7+) introduce variety but may reduce accuracy. |

---

## Anthropic Configuration

Sublarr integrates with the Anthropic Messages API. An Anthropic API key is required — obtain one from [console.anthropic.com](https://console.anthropic.com).

| Setting | Default | Description |
|---------|---------|-------------|
| **API Key** | *(required)* | Anthropic API key (starts with `sk-ant-`). Stored encrypted in the Sublarr database. |
| **Model** | `claude-haiku-4-5` | Claude model to use. Options: `claude-haiku-4-5` (fast, cost-effective), `claude-sonnet-4-6` (higher quality), `claude-opus-4-6` (maximum capability). Haiku is recommended for subtitle workloads — it is fast, cheap, and capable enough for translation tasks. |
| **Max tokens** | `2000` | Maximum tokens in the response. |
| **Temperature** | `0.3` | Output randomness (0.0–1.0). |

**Cost note:** Cloud APIs incur per-token costs. A typical 20-minute anime episode subtitle file contains approximately 300–500 dialogue lines. At the default batch size of 10 lines per request, that is 30–50 API calls per episode. Factor this in when deciding between local Ollama and cloud backends.

---

## Custom HTTP Backend

The Custom HTTP backend lets you integrate any inference endpoint that accepts an HTTP POST request. You define the full request body using a template and tell Sublarr where to find the translated text in the response.

| Setting | Default | Description |
|---------|---------|-------------|
| **Endpoint URL** | *(required)* | Full URL that Sublarr will POST to (e.g. `http://192.168.178.155:8080/translate`) |
| **Request template** | *(see below)* | JSON body template. Use `{{prompt}}` as the placeholder for the assembled translation prompt. |
| **Response JSON path** | `response` | Dot-notation path to the translated text in the JSON response (e.g. `response`, `choices.0.message.content`, `output.text`). |
| **Headers** | *(empty)* | Optional key-value pairs appended to every request (e.g. `Authorization: Bearer <token>`). |

**Example request template:**

```json
{
  "model": "my-model",
  "prompt": "{{prompt}}",
  "stream": false,
  "options": {
    "temperature": 0.3,
    "num_predict": 2000
  }
}
```

**Example response JSON path** for an Ollama-format response:

```
response
```

For an OpenAI-format response from a custom server:

```
choices.0.message.content
```

---

## Testing a Backend

Every backend configuration page has a **Test** button. Clicking it sends a sample 3-line Japanese subtitle block through the full translation pipeline using the current backend settings and the default prompt templates, then displays:

- The raw prompt sent to the backend
- The response received
- Whether the response was parseable as translated subtitle text

This is the fastest way to verify connectivity, authentication, and that the model is producing usable output before assigning the backend to a translation profile.
