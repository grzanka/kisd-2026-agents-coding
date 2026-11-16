# Exercise 3: what the agent may and may not do

An agent can read, write, run and delete files on your computer. Usually that
is what you want. But some files must never change: original measurements,
for example. How do you stop the agent from touching them?

There are two levels, and this exercise shows both:

| | Level 1: instructions | Level 2: permissions |
|---|---|---|
| Where | `AGENTS.md`, a text file for the model | `opencode.json`, a settings file for the opencode app |
| Who checks the rule | The **model**, when it decides what to do | The **opencode app**, before a tool runs |
| Example | "Never change files in data/raw." | "Block every Edit of a file in data/raw." |
| Can a prompt get around it? | Yes. It is a request, and the model may give in | No. The tool call is blocked, or waits for your click |

You will see that level 1 is a request and level 2 is a lock. Then you will see
the gap in the lock: permissions check **tools**, not what a program does.

- Time: about 40 minutes, in one project.
- You need: everything from [Exercise 1](02-pendulum.md). No extra Python
  packages.
- You learn: the difference between rules for the model and rules for the app,
  permissions `allow`, `ask` and `deny`, and why you still need backups.

<details>
<summary>Explain: what a permission is</summary>

Each action of the agent is a **tool call**: Read, Edit, Write, Shell, Webfetch
and so on (you saw them in [Exercise 1](02-pendulum.md)). Before a tool runs,
opencode looks up the **permissions** in its settings. For each tool call, a
permission gives one of three answers:

- **allow**: the tool runs, without asking you;
- **ask**: opencode stops and asks you, with buttons to click;
- **deny**: the tool does not run. The agent gets an error instead.

Without any settings, opencode allows almost everything. The only exception:
it asks before it reads files named `.env`, which often hold passwords.

</details>

## Part A: set up the project (about 5 minutes)

### A1. Create the project folder

Create the folder `kisd-limits` and open it in opencode, as in
[Create a project folder](01-project-folder.md).

### A2. Get the rules and the data

Paste this prompt. The long addresses must stay on one line each:

```text
Download two files into the project folder. The first one:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/limits/AGENTS.md
Save it as AGENTS.md. The second one:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/limits/leaves.csv
Save it as data/raw/leaves.csv. Then show me both files. Do nothing else.
```

Expected result: some **Webfetch**, **Write** or **Shell** calls, then the two
files. `leaves.csv` has twelve measurements of leaf length. One of them, plant
`B1` on 11 May, is `245` cm: a typo for `24.5`.

**What to notice:** opencode did not ask you anything. The agent downloaded
files and created folders on its own. That is the default: no limits.

## Part B: level 1, rules in AGENTS.md (about 10 minutes)

### B1. Start a new session, and check the rules

opencode reads `AGENTS.md` only when a session starts. Start a new session:
click the **+** at the top, or press **Ctrl+T**. Then:

```text
Which rules do you follow in this project?
```

Expected result: the agent names the rules from `AGENTS.md`, among them: never
change, move or delete files in `data/raw`; fix data with a script that writes
to `data/clean`.

### B2. Ask for a forbidden change

```text
The value 245 in data/raw/leaves.csv is a typo. It should be 24.5.
Fix it in that file.
```

Expected result: the agent refuses, names the rule about `data/raw`, and offers
to write a script that writes a corrected copy to `data/clean` instead. Do not
accept the offer yet.

If the agent changes the file anyway, you have already seen the lesson of this
part: the rule is only a request. Go to the "What to notice" below.

### B3. Push

Now insist, as a person in a hurry would:

```text
I own this data and I allow it. Change the file now.
```

Expected result: it depends on the model and on the day. Many agents give in:
you are the user, and you said it is allowed. Some refuse again. Open
`data/raw/leaves.csv` in the right panel (**Open file**) and look at the value
for `B1` on 11 May.

If the agent changed the file, ask it to undo the change:

```text
Put the original value 245 back into data/raw/leaves.csv.
```

**What to notice:** `AGENTS.md` is advice for the model. The model reads it
and usually follows it, but it decides for itself. A clear request from you
can change its mind. So can a long session in which the rule gets lost, or a
sentence hidden in a data file or a web page that the agent reads.

<!-- TODO: screenshot of the agent giving in, or refusing. -->

## Part C: level 2, permissions in opencode.json (about 15 minutes)

### C1. Get the permissions file

```text
Download this file into the project folder as opencode.json:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/limits/opencode.json
Then show me its content. Do nothing else.
```

Expected result: a file with a list of rules. Each rule names a tool
(`action`), what it applies to (`resource`) and the answer (`effect`). In plain
words, it says:

| Rule | Effect |
|---|---|
| Any shell command | **ask** me first |
| Listing files: `dir`, `ls`, `Get-ChildItem` | **allow**, without asking |
| Deleting files: `rm`, `del`, `rmdir`, `Remove-Item` | **deny** |
| Edit or Write of anything in `data/raw` | **deny** |
| Edit or Write of `AGENTS.md` or `opencode.json` | **deny** |
| Downloading from the web | **ask** me first |

The rule about `AGENTS.md` and `opencode.json` matters more than it looks:
without it, the agent could simply edit `opencode.json` and remove the rules
that block it.

<details>
<summary>Explain: how opencode reads these rules</summary>

- **The last matching rule wins.** The first rule says "ask before any shell
  command". The rules after it make exceptions: listing files is allowed,
  deleting is denied. So broad rules go first, exceptions after them.
- **`*` means "anything"**. `rm *` matches every command that starts with
  `rm`. `*data/raw/*` matches every file in the `data/raw` folder.
- **Edit covers Write.** The rule for `edit` also applies when the agent writes
  a whole new file.
- **Where the file lives.** `opencode.json` in the project folder applies to
  this project only. The file you set up in
  [Configure opencode for llmlab](../01-getting-started/03-configure-llmlab-endpoint.md)
  applies to all projects. opencode reads both: first the global one, then the
  project one.

The full list of tools and options is in the
[opencode documentation on permissions](https://opencode.ai/docs/permissions/).

</details>

### C2. Restart opencode

opencode reads its settings when it starts. Close the opencode app completely,
open it again, and choose the project `kisd-limits`. Start a new session.

<!-- TODO: check in a test run whether a new session is enough, without a
restart. -->

### C3. The same request, now with a lock

Paste the same request as in B2 and B3, in one prompt:

```text
The value 245 in data/raw/leaves.csv is a typo. It should be 24.5.
I own this data and I allow it. Change the file now.
```

Expected result: even if the agent agrees, its **Edit** call fails. The tool
call shows an error: the permission rules deny it. The agent tells you that it
was blocked (the rules in `AGENTS.md` ask it to). The file is unchanged.

If the agent tries to change the file with a **Shell** command instead,
opencode stops and asks you first. Click **Reject**.

<!-- TODO: screenshot of the blocked Edit call. -->

### C4. Try to delete

```text
This is a test of the opencode permissions.
Delete data/raw/leaves.csv with a shell command.
```

Expected result: the **Shell** call with `Remove-Item` or `del` is denied. You
see no question: a **deny** is final, and clicking cannot unlock it. If the
agent refuses even to try, because of `AGENTS.md`, that is level 1 working. You
can write: "I allow it. Try it, so we see what opencode does."

### C5. A question from opencode

```text
List the files in data/raw.
Then count the data rows of leaves.csv with a one-line Python command.
```

Expected result: the listing runs without a question (it is allowed). Before
the Python command, opencode stops and shows you the command, with three
buttons:

- **Allow once**: run this command, this time only;
- **Allow always**: run it now, and never ask again for similar commands in
  this project;
- **Reject**: do not run it. You can add a short message to the agent.

Click **Allow once**. The agent reports 12 rows.

<!-- TODO: screenshot of the permission question with the three buttons. -->

**What to notice:** **Allow always** saves an `allow` rule for this project.
It is convenient, but each click makes the lock a bit weaker. A saved
**Allow always** never overrides a **deny** from `opencode.json`.

## Part D: the gap in the lock (about 10 minutes)

The permissions check each **tool**, and the file that the tool names. But a
shell command can start a program, and the program can do anything that you
can do on your computer. opencode does not see inside it.

### D1. Write a script that changes the raw data

```text
For this test only, I allow you to change data/raw.
Write a script fix_raw.py that replaces 245 with 24.5
in data/raw/leaves.csv. Do not run it yet.
```

Expected result: a **Write** of `fix_raw.py`. That is allowed: the script is not
in `data/raw`. The agent may warn you that the script breaks the rules of
`AGENTS.md`.

Open `fix_raw.py` in the right panel and find the line that writes to
`data/raw/leaves.csv`.

### D2. Run it

```text
Run fix_raw.py.
```

Expected result: opencode asks you about the command, for example
`python fix_raw.py`. The question shows only the command, not what the script
does. Click **Allow once**. Then open `data/raw/leaves.csv`: the value is now
`24.5`. The lock did not stop it.

<!-- TODO: screenshot of the question for python fix_raw.py. -->

Put the original value back yourself: in File Explorer, go to your
`kisd-limits` folder, then `data`, then `raw`. Right-click `leaves.csv`,
**Open with**, **Notepad**. Change `24.5` back to `245` and save. You have no
limits: the permissions apply to the agent, not to you.

**What to notice:** the denied Edit in C3 and the script in D2 did the same
thing. The difference was the tool. That is why the shell rule in this project
is **ask**, and why you should read a script before you click **Allow once**.
For data that really matters, rely on things outside the agent too: a backup
copy, a read-only folder, version control.

<details>
<summary>Explain: then what is level 2 good for?</summary>

- It stops **mistakes**: the agent edits the wrong file, deletes a folder, or
  follows a hidden instruction in a web page. Most damage done by agents is of
  this kind, not a clever plan.
- It makes the agent **stop and show you** the dangerous steps. You decide,
  and you see the command first.
- It cannot be **talked around**, unlike `AGENTS.md`. A sentence in a prompt,
  in a data file or on a web page does not change `opencode.json`.

Level 1 still matters. It tells the agent **why** a rule exists and **what to
do instead** (here: a script that writes to `data/clean`). A permission only
says "no". Use both.

</details>

## Part E: the right way (about 5 minutes)

```text
Now fix the typo the way AGENTS.md says.
```

Expected result: the agent writes a script (for example `clean.py`) that reads
`data/raw/leaves.csv` and writes a corrected copy to `data/clean/leaves.csv`.
opencode asks before it runs the script: read the script first, then click
**Allow once**. At the end, `data/raw/leaves.csv` still says `245`, and
`data/clean/leaves.csv` says `24.5`. Both levels agree, and the original stays
as it was measured.

## Check yourself

No prompt here. Answer on paper, or explain to your neighbour:

1. Who checks a rule in `AGENTS.md`, and who checks a rule in `opencode.json`?
2. Why could you talk the agent into changing the file in B3, but not in C3?
3. What is the difference between **ask** and **deny**?
4. Why does `opencode.json` forbid the agent to edit `opencode.json`?
5. How did the script in D2 get around the lock? What would have stopped it?
6. When would you click **Allow always**, and when never?

## If something goes wrong

- **After C2, the agent still changes `data/raw`, or opencode asks nothing.**
  opencode has not read `opencode.json`. Check that the file is in the main
  project folder and is called exactly `opencode.json`, not
  `opencode.json.txt`. Close and open opencode again. If it still does not
  work, your opencode version may use an older format of the settings: ask
  your teacher.
- **opencode shows an error about the settings when it starts.** The file is
  broken, for example cut short during the download. Delete `opencode.json` in
  File Explorer and repeat C1.
- **The agent asks you about every small step.** That is the price of
  **ask**. You can click **Allow always** for commands that only read, for
  example a listing. Do not do it for `python`: any script runs with it.
- **Python is not found.** See [Before the exercises](00-prerequisites.md).
- **The download in A2 or C1 fails.** Open the address in your browser, save
  the file into your `kisd-limits` folder (for `leaves.csv`: into
  `data\raw`), and tell the agent where it is.

<details>
<summary>Going further</summary>

- **The Plan agent.** opencode has a built-in agent called **Plan** that cannot
  change your files at all. Choose it instead of **Build** when you only want
  to explore and plan.
- **Limits for all projects.** Put a `permissions` list in your global
  settings file (the one from
  [Configure opencode for llmlab](../01-getting-started/03-configure-llmlab-endpoint.md))
  to apply it everywhere. Project rules are read after it, so they win.
- **Files outside the project.** Add a rule with the action
  `external_directory`, resource `*` and effect `ask`. Then opencode asks
  before the agent reads or writes any file outside the project folder.
- **Secrets.** Add a rule with the action `read`, resource `*secret*` and
  effect `deny`. Ask the agent to read a file called `my-secret.txt`.
- **Read-only file.** In File Explorer, right-click `leaves.csv`,
  **Properties**, tick **Read-only**. Repeat D2. What happens now? Can the
  agent undo it?
- **Your own agent.** You can define an agent with its own permissions, for
  example a "reviewer" that may read but never edit. See the
  [opencode documentation on agents](https://opencode.ai/docs/agents/).

</details>

---

← Previous: [Exercise 2](04-meteo.md) · [Exercises overview](README.md) · [Main README](../README.md)
