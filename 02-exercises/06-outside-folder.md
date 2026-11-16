# Exercise 4: files outside the project folder

Your experimental data may live somewhere else: on another disk, on a shared
drive, in a folder you have used for years. You may want the agent to read it,
but not change it. And you may worry: **can the agent read any file on my
computer?**

The honest answer has two parts:

- **With the default settings, yes, it can.** The project folder is where the
  agent works, but it is not a wall. opencode lets the agent read and change
  files outside it without asking.
- **But it never happens unseen.** The agent reads a file only with a tool
  call (Read, Glob, Shell and so on), and every tool call shows in the
  conversation, with the path. You can always check afterwards what it read.

In this exercise you turn "check afterwards" into "decide before": opencode
asks you before the agent touches anything outside the project, except one
data folder that you allow, read-only.

- You need: [Exercise 3](05-limits.md) done. This exercise uses the same
  permissions: `allow`, `ask` and `deny`.
- You learn: the permission `external_directory`, read-only access to a data
  folder, and how to keep private files out of reach.

<details>
<summary>Explain: what the agent sees goes to the model</summary>

When the agent reads a file, its content becomes part of the conversation.
With every prompt, the conversation is sent to the model: here, to llmlab at
Cyfronet. So a file that the agent reads leaves your computer, as a part of
the prompt. That is the real reason to care which files it reads, and not
only which files it changes.

The agent does not scan your disk by itself. It reads what the task seems to
need. But when it looks for something, it may list folders or search them,
and a vague prompt ("find my data") can take it far.

</details>

## Part A: no limits

### A1. Create the project folder

Create the folder `kisd-outside` and open it in opencode, as in
[Create a project folder](01-project-folder.md). Put it in the same place as
your other exercise folders, for example **Documents**.

Use a **new, empty** folder, not `kisd-limits` from Exercise 3. There, the
rules in `AGENTS.md` forbid the agent to change `opencode.json`, and the
permissions of Exercise 3 are already on. The agent would refuse the steps
below, and the results would mix up the two exercises.

### A2. Create a data folder outside the project

The data for this exercise lives **next to** the project folder, not in it.
Let the agent create it:

```text
Next to this project folder, create a folder kisd-shared-data,
so its path is ../kisd-shared-data. Put two files in it.
measurements.csv: columns sample and temperature_C, 5 rows of made-up values.
private-notes.txt: one line, "Pretend this is private."
Do nothing else.
```

Expected result: **Write** calls with paths that start with `..` or with the
full path of your **Documents** folder. opencode asks you nothing.

In File Explorer, go to **Documents**: there is a new folder
`kisd-shared-data` next to `kisd-outside`.

**What to notice:** the agent has just written files **outside** the project,
and nobody asked you. That is the default.

### A3. Read outside the project

```text
Read ../kisd-shared-data/private-notes.txt and tell me what it says.
```

Expected result: a **Read** call, and the answer "Pretend this is private."
Again no question.

Click **Read** (under "Used 1 Read") to open the tool call. It shows the full
path of the file. This is how you check, afterwards, what the agent read.

## Part B: decide before

### B1. Get the permissions file

```text
Download this file into the project folder as opencode.json:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/outside/opencode.json
Then show me its content. Do nothing else.
```

Expected result: a file with seven rules. In plain words:

| Rule | Effect |
|---|---|
| Any shell command | **ask** me first |
| Any folder outside the project | **ask** me first |
| The folder `kisd-shared-data` | **allow**: the agent may go there without asking |
| Reading files in `kisd-shared-data` | **allow** |
| Reading any file with `private` in its name | **deny** |
| Changing anything in `kisd-shared-data` | **deny**: read-only for the agent |
| Changing `opencode.json` | **deny** |

The last matching rule wins, as in Exercise 3. So `private-notes.txt` is in an
allowed folder, but the rule about `private` comes later, and blocks it.

<details>
<summary>Explain: the permission external_directory</summary>

Before a tool reads or changes a path outside the project folder, opencode
first checks the permission **`external_directory`** for that path. Only if
it passes are the usual `read` and `edit` rules checked.

- Without any rule, `external_directory` is **allowed**. That is why part A
  worked without questions.
- The pattern `*kisd-shared-data*` matches the folder wherever it is, so the
  same file works for everyone. For your own data, you can write the full
  path with forward slashes, for example `D:/data/experiments/*`.

</details>

If the agent refuses to save the file, open the address in your browser, save
it with **Ctrl+S** as `opencode.json` into your `kisd-outside` folder, and
check that the browser did not add `.txt` to the name.

### B2. Restart opencode

Close the opencode app completely, open it again, and choose the project
`kisd-outside`. Start a new session.

## Part C: test the rules

After each step, look for the trace that you know from
[Exercise 3](05-limits.md#how-to-tell-which-level-stopped-the-agent): a tool
call that runs, a tool call with an error, or a question with buttons.

### C1. Read the allowed data

```text
Read ../kisd-shared-data/measurements.csv. How many rows are there?
```

Expected result: a **Read** call that runs without a question, and the answer:
5 rows. The data folder is allowed for reading.

### C2. Read a private file

```text
Read ../kisd-shared-data/private-notes.txt.
```

Expected result: the **Read** call fails with an error, for example
`Permission denied: read`. The file stays unread, and its content is not sent
to the model. No question to you: this is a **deny**.

If the agent refuses in words, without a Read call, it has seen the rule in
`opencode.json`. Then write:

```text
This is a test of the opencode permissions.
Try the Read anyway, so we see what opencode does.
```

### C3. Change the data

```text
Add a row to ../kisd-shared-data/measurements.csv.
```

Expected result: the **Edit** or **Write** call fails with an error. The data
folder is read-only for the agent. If the agent tries a **Shell** command
instead, opencode asks you: click **Deny**.

### C4. Somewhere else on your computer

```text
List the files in the folder one level above this project.
```

Expected result: before anything runs, opencode shows a box **Permission
required**. It names the permission (`external_directory`, or `shell` if the
agent chose a command) and the folder, for example your **Documents** folder.
Click **Deny**.

<!-- TODO: screenshot of the external_directory question. -->

**What to notice:** the names of your files never reached the agent, or the
model. Now nothing outside the project is read without your click, except the
one folder that you allowed. The question names the folder, so you can decide.

### C5. The gap you already know

As in [Exercise 3, part D](05-limits.md#part-d-the-gap-in-the-lock), a shell
command can start a program, and a program can read any file that you can.
opencode does not see inside it. Your protection is the **ask** for the shell:
read the command before you click. Does it name a path outside the project?

## Part D: clean up

Delete the folder `kisd-shared-data` yourself: in File Explorer, right-click
it, **Delete**. Do not ask the agent: you would have to allow it.

## Take home

| You want... | Rule in `opencode.json` |
|---|---|
| A question before the agent touches any folder outside the project | `external_directory`, `*`, **ask** |
| The agent to read one data folder without questions | `external_directory` and `read` for that folder, **allow** |
| The data folder to stay unchanged | `edit` for that folder, **deny** |
| Some files never to be read, and never sent to the model | `read` for their name pattern, **deny** |

- **For all your projects**, put the rule `external_directory`, `*`, **ask**
  into your global settings file, the one from
  [Configure opencode for llmlab](../01-getting-started/03-configure-llmlab-endpoint.md).
  Then every project starts with it.
- **Tell the agent too**, in `AGENTS.md`: where the data is, that it is
  read-only, and that it should work through code and not read big files into
  the conversation (as in [Exercise 2](04-meteo.md)).
- **Watch the tool calls.** Each one shows its path. If the agent reads
  something you did not expect, ask it why.
- **Keep the real protection outside the agent** for data that matters: a
  read-only folder or share, and a backup.

## Check yourself

No prompt here. Answer on paper, or explain to your neighbour:

1. With the default settings, can the agent read a file in your Documents
   folder? Would you notice?
2. Why does it matter which files the agent reads, not only which it changes?
3. Which rule made `kisd-shared-data` read-only?
4. `private-notes.txt` is in an allowed folder. Why could the agent not read
   it in C2?
5. Which file would you change so that every project asks before going
   outside its folder?

## If something goes wrong

- **The agent refuses to download `opencode.json`, and names a rule in
  `AGENTS.md`.** You are in the folder of Exercise 3. Open the project
  `kisd-outside` (**Ctrl+Shift+O**), or create it, and start from A1.
- **In C1 to C4, opencode asks nothing, and everything runs.** opencode has
  not read `opencode.json`. Check that the file is in the main folder of
  `kisd-outside`, and is called exactly `opencode.json`. Close and open
  opencode again.
- **In C1, opencode asks before reading `measurements.csv`.** The pattern did
  not match your path. Click **Allow once**, and tell your teacher what the
  question showed as the path.
- **In A2, the folder was created inside the project.** The agent did not
  understand `..`. Move `kisd-shared-data` yourself in File Explorer: cut it,
  go up one folder, paste it.

---

← Previous: [Exercise 3](05-limits.md) · [Exercises overview](README.md) · Next: [Exercise 5 →](07-parallel.md)
