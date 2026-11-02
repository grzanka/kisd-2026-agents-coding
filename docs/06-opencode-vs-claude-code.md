# 6. Optional: opencode and Claude Code

Read this after the exercises, when you have used opencode for a while.

Claude Code is Anthropic's coding agent. It looks and works much like opencode:
both read and edit your files, run commands and ask for permission. The main
difference is **which models they can use**.

- opencode is **open source** and works with **many model providers**, including
  PLGrid Forge. You pay only for the model, here with credits from your PLGrid
  grant, and your data stays on PLGrid hardware.
- Claude Code works only with **Anthropic's Claude models** and needs a paid
  Claude subscription or API account. Your data is sent to Anthropic.

| | opencode | Claude Code |
|---|---|---|
| Made by | Anomaly, open source (MIT licence) | Anthropic, proprietary |
| Models | Any provider: PLGrid Forge, OpenAI, local models, Claude… | Claude models only |
| Cost | Tool is free; you pay the model provider | Claude subscription or API billing |
| Where your data goes | To the provider you choose (here: PLGrid) | To Anthropic |
| Project instructions file | `AGENTS.md` (reads `CLAUDE.md` if there is no `AGENTS.md`) | `CLAUDE.md` |
| Settings file | `opencode.json` | `.claude/settings.json` |
| Plan first, then build | **Tab** switches between the `plan` and `build` agents | **Shift+Tab** cycles modes, including plan mode |
| Create the instructions file | `/init` | `/init` |
| Choose a model | `/models` | `/model` |
| Undo the last change | `/undo` (needs a Git repository) | `/rewind`, or **Esc** twice |
| Windows | Desktop app and terminal | Desktop app and terminal |

What to expect in practice: the open models on PLGrid are good, but usually
weaker than Anthropic's best Claude models at long, multi-step tasks. Give
smaller tasks, and **always check the results** (numbers, plots, file contents)
yourself.

## Sources

- opencode documentation: <https://opencode.ai/docs>
- opencode source code: <https://github.com/anomalyco/opencode>
- Claude Code documentation: <https://code.claude.com/docs>

---

← Previous: [5. First steps in opencode](05-first-steps.md) · [Docs overview](README.md) · Next: [7. For experts: other ways to set up →](07-for-experts.md)
