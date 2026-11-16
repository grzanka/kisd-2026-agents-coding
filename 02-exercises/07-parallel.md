# Exercise 5: several agents at once

So far, one agent did one thing after another. But many questions about data
do not depend on each other: frost, heat and wind can be studied at the same
time. In opencode, the main agent can hand such tasks to helpers, called
**subagents**, and they work **in parallel**.

You will ask three questions about twenty years of weather in Katowice, each
answered by its own subagent, and then let the main agent combine the
answers. On the way you will see what subagents are good for, what they cost,
and what nobody checks unless you do.

- You need: everything from [Exercise 1](02-pendulum.md). You do not need to
  have finished [Exercise 2](04-meteo.md): you download its result, the data
  as one table.
- You learn: subagents and child sessions, preparing shared things before
  parallel work, one output folder per task, what parallel work costs, and
  checking results that no agent compared.

<details>
<summary>Explain: what a subagent is</summary>

A **subagent** is a second agent that the main agent (**Build**) starts with a
tool call, as it would start a Read or a Shell. It gets a task in a prompt
that the main agent writes for it.

- It starts with an **empty conversation**, in its own **child session**. It
  does not see your earlier prompts, only its task and `AGENTS.md`.
- It works on its own: reads files, writes scripts, runs them. It cannot ask
  you questions.
- When it is done, it sends a **report** back to the main agent. The main
  agent sees the report, not every step of the subagent.
- Subagents run **in the background**, so the main agent can start several of
  them, one after another, and they work at the same time.

opencode has two built-in subagents: **General** can read, write and run
commands; **Explore** can only read and search. You can also define your own
(see "Going further").

</details>

## Part A: prepare

### A1. Create the project folder

Create the folder `kisd-parallel` and open it in opencode, as in
[Create a project folder](01-project-folder.md).

### A2. Get the rules and the data

Paste this prompt. The long addresses must stay on one line each:

```text
Download two files into the project folder. The first one:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/parallel/AGENTS.md
Save it as AGENTS.md. The second one:
https://github.com/grzanka/kisd-2026-agents-coding/releases/download/data-gig-meteo-2006-2026/meteo.parquet
Save it as data/meteo.parquet. Then show me AGENTS.md. Do nothing else.
```

Expected result: some **Webfetch**, **Write** or **Shell** calls, then the
rules of the project. `data/meteo.parquet` is about 11 MB: the whole weather
archive of 2006-2026, combined into one table, as in part B of
[Exercise 2](04-meteo.md).

If you did Exercise 2, you can copy your own `data/meteo.parquet` from the
`kisd-meteo` folder instead.

### A3. Prepare what all agents share

Start a new session (**+** or **Ctrl+T**), so that the agent reads
`AGENTS.md`. Then:

```text
Create .venv and install pandas, pyarrow and matplotlib into it.
Then check that you can read data/meteo.parquet:
print the number of rows and the first and last timestamp. Do nothing else.
```

Expected result: several **Shell** calls (this takes a minute or two), then:
**349,076 rows**, from 1 October 2006 to 2 November 2026.

**What to notice:** you install the packages **before** the subagents start.
All of them use the same `.venv`. Three agents installing packages into one
`.venv` at the same time can break it. Anything that several agents share
(the environment, a common data file) is prepared first, by one agent.

## Part B: three agents at once

### B1. Predict

Before you send the next prompt, write down two guesses:

- Will three agents in parallel be **faster** than one agent doing the three
  tasks one after another?
- Will they use **fewer or more tokens** in total?

### B2. Fan out

```text
Answer three questions about data/meteo.parquet, using full years 2007-2025.
Give each question to its own general subagent, and start all three
at the same time, in parallel.
1. Frost: days per year with a minimum temperature (Low Temp) below 0.
2. Heat: days per year with a maximum temperature (Hi Temp) above 30.
3. Wind: the most common wind direction (Wind Dir) in each season.
Each subagent writes its script, one plot and a short summary only into
its own folder: results/frost, results/heat or results/wind.
Each reports back to you in at most 5 lines. Wait for all three.
```

Expected result: the main agent makes **three subagent calls**, one for each
question, each with a short description. They run at the same time: you may
see all three working before any of them is done. After a few minutes the
reports come back, and the main agent sums them up.

<!-- TODO: screenshot of the three subagent calls. -->

If the agent answers the questions itself, one after another, without
subagents, write:

```text
Please use the subagent tool: one general subagent per question, in parallel.
```

### B3. Look inside a subagent

Click one of the subagent calls. It opens the **child session** of that
subagent: its own conversation, with its own Thoughts and tool calls (Write,
Shell). Then go back to the main session.

<!-- TODO: check in a test run how the desktop app shows child sessions, and
how to go back to the main session. -->

**What to notice:** the main session shows only the three tasks and the three
reports. All the work (scripts, errors, retries) stayed in the child sessions.

### B4. Count the tokens

In the main session, click the small circle with the token count next to the
session name, as in
[Exercise 1, step 12](02-pendulum.md#step-12-how-big-was-this-session). Then
do the same in one child session.

Expected result: the main session is small; each child session has its own,
often larger, count. Add them up: together they used **more** tokens than one
agent doing all three tasks would. Compare with your guess from B1.

<details>
<summary>Explain: then why use subagents?</summary>

- **A clean main session.** Each subagent starts empty and returns only a
  short report. The main session does not fill up with every script and error
  message, so it stays fast and focused. Remember from
  [Learning with an agent](03-learning-with-an-agent.md): a long session is
  slow, expensive and confused.
- **Time.** Independent tasks finish sooner when they run at the same time.
- **Focus.** Each subagent has one small task, with nothing else in its
  conversation.

The price: every subagent has to find out again what the main agent already
knew (where the data is, what the columns mean). That costs tokens. Parallel
work saves time and keeps the main session clean; it does not save credits.

</details>

## Part C: check what nobody compared

Each subagent saw only its own question. Nobody looked at the three results
together, and nobody was asked to check the data. That is now your job.

### C1. Look at the results

Open the plots in the right panel (**Open file**, then `results/`). Then ask:

```text
Show me the main numbers of the three results in one short table.
```

<details>
<summary>Check: what the results should be (open after you look)</summary>

- **Frost:** between about 35 and 95 frost days per year, about 60 on average.
- **Heat:** between about 4 and 32 hot days per year, about 17 on average.
- **Wind:** most likely, the subagent reports **north (N)** as the most common
  direction in **every** season. That is wrong. See C2.

</details>

### C2. Is the wind result plausible?

Wind from one direction in every season, all year round, is suspicious.
Ask:

```text
Check the wind result: for each year, what share of the rows
with Wind Speed above 0 have Wind Dir N? Show a short table.
```

Expected result: before 2019, almost no rows say `N`. From 2019 to 2024,
almost all of them do; in 2025, still about three quarters. The wind vane of the station broke in early 2019,
and since then it has reported north. Without those years, the most common
directions are **west** (`W`, `WNW`) and **east-northeast** (`ENE`), as is
usual for southern Poland.

The wind subagent did what it was asked, and its code was probably correct.
But it had one question, an empty conversation, and no reason to doubt the
data. Its report sounded sure.

**What to notice:** splitting work into subagents also splits the
**attention**. Nobody sees the whole picture unless you ask for it. Check the
reports of subagents as you would check the work of a new colleague: is the
result plausible?

### C3. Combine

```text
Write results/parallel-report.md: one paragraph and one plot per question,
the problem with the wind vane since 2019 and how we handled it,
and the source of the data.
Use the reports and the files in results/. Do not run the analyses again.
```

Expected result: a **Write** of the report, after a few **Read** calls of the
summaries in `results/`. Read the report. Is the wind problem in it?

### C4. A read-only helper

The **Explore** subagent can read and search, but not change anything:

```text
Use the explore subagent to list every file in results/
and say in one line what each one is.
```

Expected result: one subagent call, a list of files with one line each, and
no **Write** or **Edit** calls. Explore is safe for looking around: it cannot
change your files, whatever its task says.

## Take home: when to use parallel agents

| Use subagents in parallel when... | Do it with one agent when... |
|---|---|
| The tasks are **independent**: none needs another's result | Each step needs the result of the step before |
| Each task can write into **its own** folder or file | The tasks write the same file |
| The tasks are big enough to fill a session | The task is small: a subagent costs more than it saves |
| You want the main session to stay short and clean | You want to follow every step in one place |

Rules of thumb:

1. **Prepare shared things first**, with one agent: the environment, the
   packages, the data.
2. **One task = one output folder.** Two agents that write the same file
   overwrite each other's work, without any error message.
3. **Write complete tasks.** A subagent cannot ask you, and does not know your
   earlier conversation. Name the data, the columns, the years and where to
   write.
4. **Check the reports.** A short, confident report can hide a problem. Ask
   the main agent, or a subagent, to check plausibility across the results.
5. **Use Explore** when a helper only needs to look: it cannot change files.

## Check yourself

No prompt here. Answer on paper, or explain to your neighbour:

1. What does a subagent know when it starts? What does it not know?
2. Why did you install the packages before starting the subagents?
3. Why did the three subagents together use more tokens than one agent would?
   Why can they still be worth it?
4. Why did no subagent notice the broken wind vane?
5. Give one task from your own research that you would split into parallel
   subagents, and one that you would not.

## If something goes wrong

- **The agent does the three tasks itself, one after another.** Ask it
  explicitly to use the subagent tool, as in B2. Some models do this only when
  asked twice.
- **A subagent fails with an error about a package.** It may have tried to
  install something itself. Ask the main agent to install the missing package
  into `.venv`, then to start that subagent again.
- **Two subagents wrote into the same folder.** Ask the main agent which file
  comes from which subagent, and to run the affected task again, alone.
- **The download of `meteo.parquet` fails.** Open the
  [release page](https://github.com/grzanka/kisd-2026-agents-coding/releases/tag/data-gig-meteo-2006-2026)
  in your browser, download `meteo.parquet`, and put it into
  `kisd-parallel\data`.

<details>
<summary>Going further</summary>

- **Your own subagent.** Create the file `.opencode/agents/checker.md` in
  the project, with this content:

  ```text
  ---
  description: Checks results for plausibility, never changes files
  mode: subagent
  permissions:
    - action: edit
      resource: "*"
      effect: deny
  ---
  Check the results you are given for plausibility. Look for values that are
  physically impossible, constant for a long time, or that change suddenly.
  Report what you find, with numbers. Do not fix anything.
  ```

  Start a new session and ask: "Use the checker subagent to check all files in
  results/." The permissions are the ones from [Exercise 3](05-limits.md):
  this helper can never change your files.
- **Parallel sessions yourself.** Instead of subagents, open two sessions in
  the same project and give each its own task. You are then the main agent:
  you split the work and combine the results.
- **More data checks.** Ask three subagents in parallel to check three
  columns each for sensor errors, and one more to combine their findings.

</details>

---

← Previous: [Exercise 4](06-outside-folder.md) · [Exercises overview](README.md) · Next: [Exercise 6 →](08-photo.md)
