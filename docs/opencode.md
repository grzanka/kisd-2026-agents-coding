# What is opencode

opencode is an **AI coding agent** that runs in your terminal. You describe a task
in plain language, for example "load `results.csv` and plot the mean per group",
and the agent reads your files, writes and runs code, looks at the output and
fixes its own mistakes until the task is done. It asks before it does anything
risky.

In this course we use opencode with models hosted on the PLGrid supercomputers
(see [PLGrid Forge](plgrid-forge.md)).

<details>
<summary>Explain: agent, model, terminal, TUI</summary>

- **Model** (large language model, LLM): the program that reads text and writes
  text. On its own it can only answer.
- **Agent**: a program that gives a model tools (read a file, run a command, edit
  a file) and lets it use them in a loop until the task is finished.
- **Terminal**: a window where you type commands instead of clicking. On Windows,
  open **Windows Terminal** or **PowerShell** from the Start menu.
- **TUI** (text user interface): a full-screen program inside the terminal.
  opencode and Claude Code are both TUIs.

</details>

## Why opencode and not Claude Code

Claude Code is Anthropic's coding agent. It looks and works much like opencode:
both run in the terminal, read and edit your files, run commands and ask for
permission. The main difference is **which models they can use**.

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
| Quit | `/exit` | `/exit` |
| Windows | Runs natively | Runs natively |

What to expect in practice: the open models on PLGrid are good, but usually
weaker than Anthropic's best Claude models at long, multi-step tasks. Give
smaller tasks, and **always check the results** (numbers, plots, file contents)
yourself.

## Basic use

Open PowerShell in your project folder and start opencode:

```powershell
opencode
```

Expected result: the opencode screen fills the terminal, with a text box at the
bottom and the model name next to it.

Then type a task in the text box and press **Enter**. Start with a question that
changes nothing:

```text
What files are in this folder, and what do they contain?
```

Useful keys and commands inside opencode:

- **Tab**: switch between `plan` (only reads and proposes) and `build` (makes
  changes). Use `plan` first when you are unsure what the agent will do.
- `/models`: pick another model.
- `/init`: let the agent write an `AGENTS.md` that describes your project. opencode
  reads this file at the start of every session.
- `/undo`: take back the last message and its file changes.
- `/exit`: quit.

<details>
<summary>Going further</summary>

- Run one task without opening the TUI, for scripts:
  `opencode run "summarise data.csv"`. Check the result: this command can finish
  without an error even when it did nothing.
- `opencode web` opens the same agent in your browser.
- Ask a helper agent for a side task with `@`, for example
  `@general find where the plots are saved`.
- Two major versions exist, 1.x and 2.x. They use the same `opencode` command but
  differ in install and login commands. This course uses 2.x.

</details>

## Sources

- opencode documentation: <https://opencode.ai/docs>
- opencode source code: <https://github.com/anomalyco/opencode>
- Claude Code documentation: <https://code.claude.com/docs>
