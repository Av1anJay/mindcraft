# Custom OpenAI-Compatible API Endpoint

This fork adds a **dedicated profile template** (`profiles/custom-openai.json`) for using any **OpenAI-compatible API** — such as local inference servers (ollama, vLLM, llama.cpp) or third-party providers.

## How it works

The upstream `mindcraft` already supports custom endpoints natively by supplying a `"url"` field in the model object. See the [Model Specifications](https://github.com/mindcraft-bots/mindcraft#model-specifications) section in the original README.

This fork simply provides a **ready-to-use profile** (`profiles/custom-openai.json`) so you don't have to write the JSON yourself.

## Quick Start

1. Copy the template:
   ```bash
   cp profiles/custom-openai.json andy.json
   ```

2. Edit `andy.json` and replace the placeholder values:
   - `"model"`: Your model identifier on the custom endpoint (e.g., `llama3.1`, `Qwen/Qwen2.5-1.5B-Instruct`, etc.)
   - `"url"`: The base URL of your custom endpoint (must end with `/v1` for OpenAI-compatible endpoints)

   Example for a local vLLM server:
   ```json
   {
       "name": "andy",
       "model": {
           "api": "openai",
           "model": "Qwen/Qwen2.5-1.5B-Instruct",
           "url": "http://127.0.0.1:8000/v1"
       }
   }
   ```

3. In `settings.js`, ensure your profile is listed:
   ```javascript
   "profiles": [
       "./andy.json"
   ]
   ```

4. If your endpoint requires an API key, add it to `keys.json` as the **OpenAI key** (since the `openai` API handler is reused for all OpenAI-compatible endpoints):
   ```json
   {
       "OPENAI_API_KEY": "your-api-key-or-placeholder"
   }
   ```
   For local endpoints without auth, you can put any non-empty string.

5. Run:
   ```bash
   node main.js
   ```

## Supported endpoint types

Any server implementing the [OpenAI Chat Completions API](https://platform.openai.com/docs/api-reference/chat) should work. Verified examples include:

- **vLLM** — `http://localhost:8000/v1`
- **ollama** — `http://localhost:11434/v1` (with `OLLAMA_ORIGINS=* ollama serve`)
- **llama.cpp server** — `http://localhost:8080/v1`
- **OpenRouter** — `https://openrouter.ai/api/v1`
- **Any third-party OpenAI-compatible proxy**

## Using different models for different tasks

You can mix and match endpoints. For example, use a cheap local model for chat and a powerful cloud model for coding:

```json
{
    "name": "andy",
    "model": {
        "api": "openai",
        "model": "local-llama",
        "url": "http://localhost:8080/v1"
    },
    "code_model": {
        "api": "openai",
        "model": "gpt-4",
        "url": "https://api.openai.com/v1"
    }
}
```

## Troubleshooting

- **"My brain disconnected, try again."** — Check that your endpoint URL is correct, reachable, and includes `/v1` at the end.
- **Authentication errors** — Ensure `OPENAI_API_KEY` in `keys.json` is set (can be a dummy value for local endpoints).
- **Model not found** — The `model` string must match what your endpoint exposes (check `/v1/models` on your server).

## Original Project

This is a fork of [mindcraft-bots/mindcraft](https://github.com/mindcraft-bots/mindcraft). All original features and documentation remain intact.
