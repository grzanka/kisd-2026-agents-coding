# 5. First steps in opencode

## Open a project folder

opencode always works in one folder, called the **project**. It can read and
change only the files in that folder. You see the current project under the text
box; right after installing it is **Default Project**.

For real work, use a separate folder for each task. Click the project name under
the text box and choose your folder. The exercises tell you which folder to use.

<!-- TODO: add a screenshot of choosing a project folder in the desktop app. -->

## Ask the first question

Type a task in the text box and press **Enter**. Start with a question that
changes nothing:

```text
What files are in this folder, and what do they contain?
```

Expected result: within a few seconds the agent lists the files. In an empty
folder it says that the folder is empty.

Useful keys and commands in the text box (type `/` to see them all):

- **Tab**: switch between `plan` (only reads and proposes) and `build` (makes
  changes). Use `plan` first when you are unsure what the agent will do.
- `/models`: pick another model.
- `/init`: let the agent write an `AGENTS.md` that describes your project. opencode
  reads this file at the start of every session.
- `/undo`: take back the last message and its file changes.

## Which model

| Model (as shown in opencode) | Model id on llmlab | Notes |
|---|---|---|
| DeepSeek V4.1 Flash (PLGrid) | `deepseek-ai/DeepSeek-V4.1-Flash` | Default. Fast, large context, good at agentic work. **Only some grants can use it.** |
| Gemma 4 31B (PLGrid) | `google/gemma-4-31B` | Fallback. Also used for the short session titles. |

If you see the error *"not available for grant 'N'"*, your grant cannot use the
default model. Pick **Gemma 4 31B (PLGrid)** in the model list, or ask the PLGrid
Helpdesk for access.

> [!IMPORTANT]
> The model list also contains free models run by opencode, among them a
> **DeepSeek V4.1 Flash** without "(PLGrid)". Use only the models that end with
> **(PLGrid)**.

Only use models marked **FC** (function calling) on <https://llmlab.plgrid.pl>.
An agent needs function calling to run commands and edit files.

## Good habits

- **Check the results yourself.** Models can write code that runs but computes the
  wrong thing, or claim that tests pass when they do not. Look at the numbers and
  plots before you trust them.
- **Watch your credits.** Long agent sessions use many credits. Keep the monthly
  limit on your key.
- **A quiet finish is not a success.** If opencode stops without an error, check
  that the expected files or results actually exist.

<details>
<summary>Going further</summary>

- Ask a helper agent for a side task with `@`, for example
  `@general find where the plots are saved`.
- Test your key without opencode, store it as an environment variable, or use
  the terminal version of opencode: see
  [7. For experts: other ways to set up](07-for-experts.md).

</details>

## Sources

- Model limits in our settings file were taken from the community setup
  <https://github.com/groundnuty/plgrid-llmlab-opencode> (unofficial, checked 2026-11).

---

← Previous: [4. Configure opencode for llmlab, part 2: the API key](04-configure-llmlab-api-key.md) · [Docs overview](README.md) · Next: [Exercises →](../exercises/README.md)
