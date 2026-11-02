# 1. What is opencode

opencode is an **AI coding agent**. You describe a task in plain language, for
example "load `results.csv` and plot the mean per group", and the agent reads
your files, writes and runs code, looks at the output and fixes its own mistakes
until the task is done. It asks before it does anything risky.

In this course we use the **opencode desktop app**: you install it and start it
like any other program, from the Start menu (Windows), Launchpad (macOS) or the
application menu (Linux). It uses models hosted on the PLGrid supercomputers
(see [3. Get access to llmlab](03-get-llmlab-access.md)).

<details>
<summary>Explain: agent, model, terminal</summary>

- **Model** (large language model, LLM): the program that reads text and writes
  text. On its own it can only answer.
- **Agent**: a program that gives a model tools (read a file, run a command, edit
  a file) and lets it use them in a loop until the task is finished.
- **Terminal**: a window where you type commands instead of clicking. opencode
  also exists as a terminal program. We do not use it in this course.

</details>

## Why opencode and not Claude Code

Claude Code is Anthropic's coding agent. It looks and works much like opencode:
both read and edit your files, run commands and ask for permission. The main
difference is **which models they can use**.

- opencode is **open source** and works with **many model providers**, including
  PLGrid Forge. You pay only for the model, here with credits from your PLGrid
  grant, and your data stays on PLGrid hardware.
- Claude Code works only with **Anthropic's Claude models** and needs a paid
  Claude subscription or API account. Your data is sent to Anthropic.

## Side by side

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

[Docs overview](README.md) · Next: [2. Install the opencode app →](02-install-opencode.md)
