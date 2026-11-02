# Guidelines for creating content in this repo

This repo holds lecture materials on **agentic coding for data analysis**, using
**opencode** and **llmlab at Cyfronet**. The course is for PhD students of the
KISD doctoral school.

## 1. Audience

The audience is mixed, and the range is wide:

- Some participants have only basic MS Office-level computer skills. Most of them
  use **Windows**.
- Some are experienced programmers who code daily or work part time in software houses.

Write so that both groups are served:

- **Assume no prior terminal, Git, or Python experience** in the main path. Say
  what a terminal is, where to open it on Windows, and what a successful result
  looks like.
- **Do not talk down to experienced readers.** Keep the main path short. Put
  background in collapsible "Explain" sections and extras in "Going further"
  sections, so experts can skip the basics and beginners can open them.
- **Windows first.** Give Windows instructions as the default (PowerShell or
  Windows Terminal; mention WSL only as an option). Add Linux/macOS variants
  beside them, not instead of them.
- Define jargon on first use (agent, token, prompt, shell, repo, environment).
- State the expected output after each step, so a beginner can tell whether it worked.
- Say what to do when a step fails: the most common errors and their fixes.

## 2. Copy-paste blocks

- **One command per code block.** Never put two commands in one block, even when
  they belong together. Each block is copied and pasted on its own.
- Put the explanation **outside** the block, before or after it.
- Do not include prompt characters (`$`, `>`, `PS>`) inside the block. They break copy-paste.
- Do not include output in the same block as the command. Show the expected
  output as a **screenshot** of the terminal or app, not as a code block: a code
  block gets a copy button on GitHub and invites copying the output.
- Do not use line continuations (`\`, `` ` ``) to make one long command look like
  several. Prefer a single line, or a script file.
- Label the shell above each block when it matters (PowerShell, bash).
- Prompts meant for the agent follow the same rule: one prompt per block.

Example of the right form:

Create the project folder:

```powershell
mkdir kisd-demo
```

Go into it:

```powershell
cd kisd-demo
```

## 3. Folder READMEs

- **Every folder has a short `README.md`** that says what is in it and in what
  order to read it. Keep it short: one or two sentences, then a table of the
  files with one line each.
- When you add, rename or remove a file, update the README of its folder and the
  main `README.md`.
- End each folder README with a link back to the main README.

## 4. Order of pages

- Top-level folders have a two-digit prefix in the order participants use them:
  `01-getting-started/`, `02-exercises/`. Files that pages show or download
  (screenshots, settings files) go in an unnumbered subfolder of the folder that
  uses them: `01-getting-started/images/`, `01-getting-started/config/`.
- Pages in `01-getting-started/` and `02-exercises/` have a two-digit prefix in reading order:
  `01-chatbot-or-agent.md`, `02-install-opencode.md`, … One page covers one
  stage of the setup or one exercise.
- Images in `01-getting-started/images/` start with the number of the page that uses them,
  then the step: `03-endpoint-2-mkdir.png`.
- Embed wide screenshots (browser pages, app windows) with
  `<img src="..." alt="..." width="700">`, so they line up with the tables
  instead of filling the whole page width.
- End each page with a navigation line after `---`:
  `← Previous: [..] · [Overview](README.md) · Next: [.. →]`.
- When you insert, remove or reorder a page, renumber the files and fix every
  link to them, including the previous/next lines.
