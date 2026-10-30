# Exercise 1: Your first agent session — measuring *g* with a pendulum

In a chat (ChatGPT, Copilot chat, the llmlab web page) you ask, the model
answers, and **you** copy the code, run it, and paste the errors back. An agent
does that loop **itself**: it writes files, runs them, reads the output, and
fixes what went wrong.

In this exercise you watch that happen on a small physics task. A student lab
measured how long pendulums of different lengths take to swing. From that you
compute the gravitational acceleration *g* (about 9.81 m/s²). The data has two
traps in it, as real lab data often does. You will see how the agent deals with
them.

You do **not** need to write or read any Python. You only type prompts and
watch.

- Time: about 30–40 minutes.
- You need: opencode connected to PLGrid Forge (see
  [PLGrid Forge](../docs/plgrid-forge.md)).

<details>
<summary>Explain: agent, tool, tool call, agentic loop</summary>

- **Model**: the program that reads text and writes text. On its own it cannot
  see your computer.
- **Tool**: an action the agent offers the model: read a file, write a file, edit
  a file, run a command in the terminal.
- **Tool call**: the model asks for one of those actions, for example "run
  `python analyse.py`". The agent carries it out and sends the result back to the
  model.
- **Agentic loop**: the model thinks, calls a tool, looks at the result, and
  decides what to do next. It repeats this until the task is done, then answers
  you.

</details>

## What to watch for

While the agent works, opencode shows each tool call as a short line in the
conversation, with the tool name and its target, for example:

- `Read` / `List` / `Glob`: the agent looks at files.
- `Write`: the agent creates a new file.
- `Edit`: the agent changes part of an existing file. opencode shows the change
  in red (removed) and green (added).
- `Bash`: the agent runs a command in the terminal and reads its output. (It is
  called `Bash` on Windows too.)

After each step below, count which of these you saw. That is the whole point of
the exercise.

If opencode asks for **permission** before a tool call, read what it wants to do,
then approve it.

## Step 0: Prepare the folder

Open **Windows Terminal** (or **PowerShell**) from the Start menu. It opens in
your user folder, for example `C:\Users\anna`.

Create a folder for the exercise:

```powershell
mkdir kisd-pendulum
```

Go into it:

```powershell
cd kisd-pendulum
```

Copy the opencode setup files into this folder (the `.opencode` folder,
`AGENTS.md` and `opencode.json`), as described in
[PLGrid Forge, step 2](../docs/plgrid-forge.md#2-connect-opencode).

Start opencode:

```powershell
opencode
```

Expected result: the opencode screen opens, with a text box at the bottom and the
model name next to it.

The commands are the same in bash on Linux and macOS.

## Step 1: A plain question (chat mode)

Paste this into the opencode text box and press **Enter**:

```text
What is the formula for the period of a simple pendulum, and how can I use it to measure g? Answer in three sentences.
```

Expected result: a short answer with T = 2π√(L/g), so g = 4π²L/T².

**What to notice:** no tool calls. The model answered from what it knows, exactly
like a chat. Everything from here on is different.

## Step 2: Look around (read-only tools)

```text
Do not change anything. List the files in this folder, and check which version of Python is installed on this computer.
```

Expected result: the agent lists `.opencode`, `AGENTS.md` and `opencode.json`,
and reports a Python version such as `Python 3.12.4`.

**What to notice:** the model cannot see your disk. To answer, it had to call a
tool to list the files and a `Bash` tool to run `python --version`, then read
the results. You did not tell it which commands to use.

<details>
<summary>If Python is not installed</summary>

On Windows the agent may report *"Python was not found; run without arguments to
install from the Microsoft Store"*. Quit opencode with `/exit`, then install
Python in PowerShell:

```powershell
winget install -e --id Python.Python.3.12
```

Close the terminal, open a new one, go back into the folder with
`cd kisd-pendulum`, start `opencode` again and repeat this step.

On Linux, install the `python3` package with your package manager. On macOS, run
`brew install python`.

</details>

## Step 3: Create the data file (`Write`)

The data below was typed into Polish Excel and exported as CSV, so it uses
semicolons and decimal commas. Paste the whole prompt, including the data:

```text
Create a file named pendulum.csv with exactly the content below. It comes from a student lab: each row is one pendulum, with the string length in metres and the time of 10 full swings in seconds, measured with a stopwatch. Do not change any values.

length_m;time_10_swings_s
0,20;8,9
0,30;11,0
0,40;12,7
0,50;14,1
0,60;15,4
70;16,8
0,80;18,1
0,90;19,1
1,00;20,2
1,10;21,1
1,20;22,0
1,30;22,9
```

opencode may show the pasted text as a single block, such as
`[Pasted ~14 lines]`. That is fine.

Expected result: a `Write` tool call for `pendulum.csv`. Open File Explorer and go
to your `kisd-pendulum` folder: the file is there. You can open it in Notepad.

**What to notice:** the agent created a real file on your disk. In a chat you
would have to copy the text and save the file yourself.

## Step 4: Analyse (`Write` + `Bash` + the loop)

```text
Write a short Python script named analyse.py that reads pendulum.csv and, for each row, computes the period T (time of 10 swings divided by 10) and g = 4 * pi^2 * length / T^2. Print a table with one row per pendulum, then the mean of g. Use only the Python standard library. Run the script and show me the output.
```

Expected result: a table of 12 rows, then a mean *g* of about **90 m/s²**. That is
far too large. Keep it for the next step.

**What to notice:** several tool calls in a row: `Write` for the script, then
`Bash` to run `python analyse.py`. The agent read the output before answering
you.

Often the first run fails, because Python does not read `0,20` as a number. You
then see an error such as `ValueError: could not convert string to float: '0,20'`,
followed by an `Edit` of the script and a second run, without you doing anything.
That is the agentic loop: act, look at the result, fix, try again. If it did not
happen to you, look at your neighbour's screen.

## Step 5: Find the problem (reasoning on results)

```text
The mean g is far from 9.81 m/s^2. Look at the per-row results and find out why. Do not change any files yet, just explain.
```

Expected result: the agent points to the row with length `70`. Its *g* is about
979, a hundred times too large: the length was written in **centimetres**, not
metres.

**What to notice:** the agent used the output of its own script as evidence. It
may also read `pendulum.csv` again to check.

## Step 6: Fix it (`Edit`)

```text
That length was written in centimetres. Fix it in analyse.py, not in pendulum.csv: convert any length above 10 to metres and print a warning when you do. Also print the standard error of the mean of g. Run the script again.
```

Expected result: an `Edit` of `analyse.py` with red and green lines, a new run,
a warning about the row with `70`, and a mean *g* of about **9.80 m/s²** with a
standard error of about **0.03 m/s²**.

**What to notice:** the agent changed **part** of an existing file instead of
rewriting it, and checked its change by running the script again.

<details>
<summary>Explain: why fix the script and not the data?</summary>

`pendulum.csv` is the raw data. If you correct it by hand, nobody can later see
that a correction was made. The correction in the script is written down, prints
a warning, and runs the same way every time. This is good practice in any data
analysis, with or without an agent.

</details>

## Step 7: Plot (`Edit` + installing a package)

```text
Add a plot to analyse.py: the measured period T against the length as points, and the curve T = 2*pi*sqrt(L/g) with the mean g as a line. Label the axes with units. Save it as pendulum.png. Use matplotlib and install it if it is missing. Run the script.
```

Expected result: the agent probably runs `python -m pip install matplotlib`
first (opencode may ask you to allow it), then edits and runs the script. A new
file `pendulum.png` appears in the folder. Open it: the 12 points lie on or very
close to a smooth rising curve.

**What to notice:** the agent noticed a missing package and installed it. It
handled the environment, not only the code.

## Step 8: Write the report (`Write` from results)

```text
Write report.md: a half-page lab report with the aim, the method (the formula), a note about the centimetre correction, the result for g with its standard error, a comparison with 9.81 m/s^2, and the plot pendulum.png embedded.
```

Expected result: a new file `report.md`. Open it in Notepad (or, better, in VS
Code, which shows the plot). The numbers in it should match what the script
printed in step 6. **Check this yourself.**

## Step 9: Ask the agent what it did

```text
List, in order, every tool you used in this session, and for each one say in one sentence why you needed it.
```

Expected result: a list such as read, write, run, edit, run, install… Compare it
with the tool calls you saw on screen. Do they match?

Quit opencode:

```text
/exit
```

## What just happened

Every step above followed the same loop:

```mermaid
flowchart LR
    A[Your prompt] --> B[Model thinks]
    B --> C{Done?}
    C -- no --> D[Tool call:<br/>read, write, edit, run]
    D --> E[Result:<br/>file content, output, error]
    E --> B
    C -- yes --> F[Answer to you]
```

| | Chat | Agent |
|---|---|---|
| Sees your files | Only what you paste | Reads them itself |
| Runs code | No, you do | Yes, and reads the output |
| Fixes its errors | Only if you paste the error back | Yes, in the loop |
| Leaves results | Text in a chat window | Files on your disk: `analyse.py`, `pendulum.png`, `report.md` |
| Your job | Copy, paste, run | Give clear tasks, **check the results** |

The last row matters most. The agent can be confidently wrong. It found the
centimetre error here because the number was absurd. A small error, say 0,60
typed as 0,61, would pass silently. You are still the scientist.

## If something goes wrong

- **The agent only describes what it would do but does not do it.** You may be in
  the `plan` agent. Press **Tab** to switch to `build` and send the prompt again.
- **"not available for grant"**: your grant cannot use the default model. See
  [PLGrid Forge, step 3](../docs/plgrid-forge.md#3-which-model).
- **`pip` fails with a network or proxy error.** Ask the agent to try again; if it
  keeps failing, skip the plot and do step 8 without it.
- **The agent stops without a result.** Check whether the expected file exists.
  If not, send the same prompt again, or add "Run the script and show me the
  output" at the end.
- **The numbers differ a little from those above.** Small differences (for example
  9.79 instead of 9.80) are fine. A different order of magnitude is not: ask the
  agent to explain.

<details>
<summary>Going further</summary>

- **Change the data yourself.** Open `pendulum.csv` in Notepad, add a row
  `1,40;23,8`, save, and ask the agent to "run the analysis again". It reads the
  current file, not what it remembers.
- **Plan first.** Press **Tab** to switch to `plan`, then ask: "How would you
  estimate g from a straight-line fit of T^2 against L instead of the mean?"
  The agent only reads and proposes. Press **Tab** again to `build` and let it
  do it. Does the fit give the same *g*? Why might it not?
- **Undo.** `/undo` takes back the last message and its file changes. It needs a
  Git repository: ask the agent to "initialise a git repository and commit all
  files" first.
- **Look at the code.** Open `analyse.py`. It is about 30–50 lines. Ask the
  agent to explain any line you do not understand.

</details>

[← Back to the main README](../README.md)
