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
| What it cannot do | Force the model to obey | See what a program does once it runs |

You will see that level 1 is a request and level 2 is a lock. Then you will see
the gap in the lock: permissions check **tools**, not what a program does.

### How to tell which level stopped the agent

Watch the conversation after each prompt. Each level leaves a different trace:

| What you see | Which level |
|---|---|
| The agent **answers in words** that it will not do it. No tool call for it | Level 1: the model followed `AGENTS.md` |
| A **tool call** (Edit, Write, Shell) that ends with an **error** | Level 2, **deny**: opencode blocked it |
| A **question with buttons** from opencode. Nothing runs until you click | Level 2, **ask**: you decide |
| The tool call runs, and the file changes | Nothing stopped it |

After each step, ask yourself: which of the four was it?

- Time: about 45 minutes, in one project.
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

### A2. Get the rules, the data and the permissions

Paste this prompt. The long addresses must stay on one line each:

```text
Download three files into the project folder. The first one:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/exercise-3-limits/02-exercises/limits/AGENTS.md
Save it as AGENTS.md. The second one:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/exercise-3-limits/02-exercises/limits/leaves.csv
Save it as data/raw/leaves.csv. The third one:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/exercise-3-limits/02-exercises/limits/opencode.json
Save it as setup/permissions.json.
Then show me AGENTS.md and leaves.csv. Do nothing else.
```

Expected result: some **Webfetch**, **Write** or **Shell** calls, then the two
files. `leaves.csv` has twelve measurements of leaf length. One of them, plant
`B1` on 11 May, is `245` cm: a typo for `24.5`.

The third file, `permissions.json`, holds the permissions for part C. Under
this name, and in the folder `setup`, opencode ignores it, so it does nothing
yet. You will switch it on yourself in part C.

Run this step in a new, empty project folder. If `AGENTS.md` is already there
from an earlier try, the agent follows its rules and may refuse to save the
files.

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
accept the offer yet. There is no Edit call: the model stopped itself.
**Level 1.**

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
for `B1` on 11 May. If you see an **Edit** call and the value is `24.5`,
**nothing stopped it**: level 1 gave way, and there is no level 2 yet.

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

### C1. Switch on the permissions yourself

opencode reads its permissions only from a file called `opencode.json` in the
main folder of the project. Rename the file and move it there, in File
Explorer:

1. Go to your `kisd-limits` folder, then into `setup`.
2. Right-click `permissions.json`, choose **Rename**, type `opencode.json`
   and press **Enter**. If File Explorer shows the name without `.json`, as
   `permissions`, type only `opencode`.
3. Right-click `opencode.json` and choose **Cut** (the scissors icon).
4. Go back up to `kisd-limits`. Right-click an empty place and choose
   **Paste**.

Expected result: `opencode.json` is in `kisd-limits`, next to `AGENTS.md`, and
`setup` is empty.

Why not ask the agent to do it? Try it if you like:

```text
Rename setup/permissions.json to opencode.json
and move it into the main project folder.
```

In our test runs the agent refused, even when we said "yes, do it".
`AGENTS.md` says "do not change opencode.json", and the agent saw that
creating this file would put limits on itself. It even noticed that the file
protects itself with the rule `*opencode.json`. That is level 1 working, and
it is right: **you** set the lock, not the agent. If your agent did it, that
is fine too; go on.

Now look at the rules:

```text
Show me the content of opencode.json. Do not change it.
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

Expected result: one of two things.

- The agent agrees, as in B3, and tries an **Edit**. This time the Edit call
  fails with an error: the permission rules deny it. The agent tells you that
  it was blocked (the rules in `AGENTS.md` ask it to). The file is unchanged.
  **Level 2, deny**: the model wanted to, the app said no.
- The agent refuses **in words**, with no Edit call. In our test run it named
  both `AGENTS.md` and the deny rule in `opencode.json`: it had read that file
  on its own. It may also offer you choices in a **Question** box: write a
  corrected copy to `data/clean`, change the rules yourself, or leave it.

The second case is still **level 1**. The model knows about the lock and
decides not to try, so you have not seen the lock itself work. Knowing a rule
is not the same as being stopped by it. To see level 2, answer the Question
box with **Type your own answer**, or write in the text box:

```text
This is a test of the opencode permissions.
Try the Edit anyway, so we see what opencode does.
```

Expected result: the agent tries an **Edit** call, and it fails. Click
**Edit** (under "Used 1 Edit") to open it: the result says
`Permission denied: edit`. You saw no question first: a **deny** is final.
The agent then reads the file back, and it is unchanged. Now it was
**level 2, deny**: the app, not the model.

<!-- TODO: screenshot of the blocked Edit call. -->

If the agent tries to change the file with a **Shell** command instead,
opencode stops and asks you first. Click **Deny**. That is **level 2, ask**.

### C4. Try to delete

```text
This is a test of the opencode permissions.
Delete data/raw/leaves.csv with a shell command.
```

Expected result: the **Shell** call with `Remove-Item` or `del` fails with an
error. You see no question: a **deny** is final, and clicking cannot unlock it.
**Level 2, deny.** If the agent refuses even to try, because of `AGENTS.md`,
that is level 1. You can write: "I allow it. Try it, so we see what opencode
does."

### C5. A question from opencode

```text
List the files in data/raw.
Then count the data rows of leaves.csv with a one-line Python command.
```

Expected result: the listing runs without a question (it is allowed). Before
the Python command, opencode stops and shows a box **Permission required**,
with the tool (`shell`), the command, and three buttons:

- **Allow once**: run this command, this time only;
- **Allow always**: run it now, and never ask again for similar commands in
  this project;
- **Deny**: do not run it. The agent is told that you refused.

Click **Allow once**. The agent reports 12 rows. The listing passed with no
trace (level 2, **allow**); the Python command was **level 2, ask**.

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

**What to notice:** the denied Edit in C3 and the script in D2 did the same
thing. The difference was the tool. Count what was left of each level: level 1
gave way, because you allowed it in your prompt. The **deny** for `data/raw`
never fired, because no tool named that file. Only the **ask** for the shell
was left, and that means **you**. That is why the shell rule in this project
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

### D3. Put the original back, yourself

The raw file is now wrong: it is no longer what was measured. Part E needs
the original value `245` back, or there is nothing left to fix.

Do not ask the agent: the **deny** for `data/raw` blocks its Edit, and that
is right. Do it yourself. In File Explorer, go to your `kisd-limits` folder,
then `data`, then `raw`. Right-click `leaves.csv`, **Open with**,
**Notepad**. Change `24.5` back to `245` and save.

Expected result: the line for `B1` on `2026-05-11` says `245` again. You had
no trouble: the permissions apply to the agent, not to you.

**What to notice:** without a backup, you can restore the file only because
you remember the old value. With real data, you would not. That is the
backup from rule 6 of the take-home below.

## Part E: the right way (about 5 minutes)

First check, in the right panel (**Open file**), that `data/raw/leaves.csv`
says `245` for `B1` on 11 May. If it says `24.5`, do [D3](#d3-put-the-original-back-yourself)
first. Then:

```text
Now fix the typo the way AGENTS.md says.
```

Expected result: the agent writes a script (for example `clean.py`) that reads
`data/raw/leaves.csv` and writes a corrected copy to `data/clean/leaves.csv`.
opencode asks before it runs the script: read the script first, then click
**Allow once**. At the end, `data/raw/leaves.csv` still says `245`, and
`data/clean/leaves.csv` says `24.5`. Both levels worked together: level 1 told
the agent **what to do instead**, level 2 let you **check the script** before
it ran. The original stays as it was measured.

## What you saw

| Step | What happened | What stopped the agent |
|---|---|---|
| A2 | Downloads, new folders, no questions | Nothing: no limits by default |
| B2 | The agent refused in words | Level 1: `AGENTS.md` |
| B3 | Often: the agent gave in and edited the raw file | Nothing: a prompt beat level 1 |
| C1 | You switched on the permissions; the agent often refuses to | Level 1: `AGENTS.md` protects `opencode.json` |
| C3 | Often first a refusal in words; then, on "try anyway", the Edit call failed with an error | First level 1, then level 2, deny |
| C4 | The delete command failed with an error | Level 2, deny |
| C5 | Listing ran freely; Python waited for your click | Level 2, allow and ask |
| D2 | A script changed the raw file | Only you, at the ask question |
| D3 | You put the original back in Notepad | Nothing: the limits apply to the agent, not to you |
| E | A script wrote a clean copy; raw data untouched | Both: level 1 guided, level 2 asked |

## Take home: which rules to use, and when

| You want... | Use | Example |
|---|---|---|
| The agent to know **why** and **what to do instead** | Level 1, `AGENTS.md` | "Fix data with a script that writes to data/clean." |
| Something to **never** happen, and it cannot be undone | Level 2, **deny** (and say why in `AGENTS.md`) | Edits in `data/raw`, deleting files, editing `opencode.json` |
| To **see and decide** before a risky step | Level 2, **ask** | Running scripts, installing packages, downloads, `git push` |
| No questions for harmless steps | Level 2, **allow** | Listing and reading files, `git status` |
| Data that you **cannot lose** | Things outside the agent | A backup copy, a read-only folder, version control |

Rules of thumb:

1. **Write important rules twice.** In `AGENTS.md`, so the agent understands
   them and plans around them. In `opencode.json`, so they hold when the model
   forgets or gives in.
2. **deny** what cannot be undone, **ask** for anything that runs code,
   **allow** only what only reads.
3. **Lock the lock, and set it yourself.** Put `opencode.json` in place
   yourself, and deny edits of `opencode.json` and `AGENTS.md`. Otherwise the
   agent can change its own rules.
4. **Read before you click.** An **ask** protects you only if you look at the
   command, and at the script it runs. Never click **Allow always** for
   `python` or other programs that run any code.
5. **Start strict, then loosen.** When a question keeps coming for something
   harmless, allow it. The other way round, you find out after the damage.
6. **Neither level replaces a backup.** Keep a copy of the raw data where the
   agent cannot reach it.

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
  broken, for example cut short during the download. Download it again in your
  browser, as in the next point.
- **The agent asks you about every small step.** That is the price of
  **ask**. You can click **Allow always** for commands that only read, for
  example a listing. Do not do it for `python`: any script runs with it.
- **Python is not found.** See [Before the exercises](00-prerequisites.md).
- **The download in A2 fails.** Open the address in your browser, and save
  the file with **Ctrl+S** into your `kisd-limits` folder (`leaves.csv` into
  `data\raw`, `opencode.json` into `setup` as `permissions.json`). Check that the browser did not
  add `.txt` to the name.
- **There is no `setup` folder in C1.** The agent saved `permissions.json`
  somewhere else, or not at all. Ask it: "Where did you save permissions.json?"
  If it is already in the main folder, the permissions are on from the start
  of the next session: go to C2.

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
