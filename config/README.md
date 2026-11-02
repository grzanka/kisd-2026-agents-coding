# Config

A ready-made opencode configuration that connects opencode to PLGrid Forge
(llmlab). How to install it: [Step by step: connect the opencode app to llmlab](../docs/opencode-llmlab-setup.md).

| File | What it does |
|---|---|
| [opencode.json](opencode.json) | Adds the llmlab endpoint with two models: `deepseek-ai/DeepSeek-V4.1-Flash` (default) and `google/gemma-4-31B` (fallback, also used for session titles). Reads the API key from the `PLGRID_API_KEY` environment variable. |

[← Back to the main README](../README.md)
