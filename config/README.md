# Config

A ready-made opencode configuration that connects opencode to PLGrid Forge
(llmlab). How to install it: [3. Configure opencode for llmlab, part 1](../01-getting-started/03-configure-llmlab-endpoint.md).

| File | What it does |
|---|---|
| [opencode.json](opencode.json) | Adds the llmlab endpoint with two models: `deepseek-ai/DeepSeek-V4.1-Flash` (default) and `google/gemma-4-31B` (fallback, also used for session titles). Takes the API key from the opencode app, or (for experts) from the `PLGRID_API_KEY` environment variable. |

[← Back to the main README](../README.md)
