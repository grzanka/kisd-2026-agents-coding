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
- You need: the opencode app connected to llmlab (pages 2–4 of
  [Getting started](../01-getting-started/README.md)), Python installed
  ([Before the exercises](00-prerequisites.md)), and an empty project folder
  ([Create a project folder](01-project-folder.md)).

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

Above each answer, opencode shows a summary line such as *Used 2 Read, Shell*.
Click it (the small ▾) to open the details: each tool call on its own line, with
the tool name and its target. The tools you will see:

- **Read** (also *List* or *Glob*): the agent looks at files or folders.
- **Write**: the agent creates a new file, or writes a whole file again.
  opencode shows its content with the new lines in green.
- **Edit**: the agent changes part of an existing file. opencode shows the change
  in red (removed) and green (added).
- **Shell**: the agent runs a command in the terminal and reads its output.

You will also see **Thought**: the model thinking before it acts. It is not a
tool call; nothing happens on your computer. Open the Thoughts: they show why
the agent did what it did.

<details>
<summary>Picture: the conversation with all details closed</summary>

<img src="images/02-pendulum-0-collapsed.png" alt="The opencode conversation with every Used line closed: only the prompts, the Used lines and the answers are visible" width="700">

</details>

After each step below, count which tools you saw. That is the whole point of the
exercise.

The pictures on this page come from one test run. Your run will differ: the
model words its answers differently each time, may pick other tools, and may
write a longer or shorter script. The numbers should be the same.

If opencode asks for **permission** before a tool call, read what it wants to do,
then approve it.

## Before you start: open the project

Create the folder `kisd-pendulum` and open it as the project in opencode, as in
[Before each exercise: create a project folder](01-project-folder.md).

Expected result: under the text box you see the project `kisd-pendulum`, and the
model **PLGrid Forge (Cyfronet) / DeepSeek V4.1 Flash (PLGrid)**.

## Step 1: A plain question (chat mode)

Paste this into the opencode text box and press **Enter**:

```text
What is the formula for the period of a simple pendulum,
and how can I use it to measure g?
Answer in three sentences.
```

Expected result: *Used 1 Thought*, then a short answer with T = 2π√(L/g), so
g = 4π²L/T².

<details>
<summary>Picture: the answer in the test run</summary>

<img src="images/02-pendulum-1-chat-answer.png" alt="Used 1 Thought, opened: the model plans to just answer; then three sentences with T = 2π√(L/g), g = 4π²L/T², and a note that this holds for small swings below about 15°" width="700">

</details>

**What to notice:** no tool calls, only a Thought. The model answered from what
it knows, exactly like a chat. In the test run it even added a condition you did
not ask for: the formula holds only for small swings. Everything from here on is
different.

## Step 2: Look around (read-only tools)

```text
Do not change anything.
List the files in this folder,
and check which version of Python is installed on this computer.
```

Expected result: *Used 2 Read, Shell*, then the answer: the folder is empty,
and the Python version, such as `Python 3.15.0`. Open the line to see the
details: **Read** of your project folder, and **Shell** with the command
`python --version` and its output.

<details>
<summary>Picture: Read and Shell in the test run</summary>

<img src="images/02-pendulum-2-read-shell.png" alt="Used 2 Read, Shell, opened: Thought, Read of the project folder, Shell python --version with the output Python 3.15.0, Thought; then the answer: the folder is empty, Python 3.15.0, nothing changed" width="700">

</details>

**What to notice:** the model cannot see your disk. To answer, it had to call
**Read** to list the files and **Shell** to run `python --version`, then read
the results. You did not tell it which commands to use. Some runs use **Glob**
instead of Read, or check for hidden files too; the result is the same.

Look at the last Thought: the model calls Python 3.15.0 a *fictional future
version*. The model was trained before that version came out, so it does not
know it. It trusted the output of the command over its own memory, which is
right.

<details>
<summary>If Python is not installed</summary>

On Windows the agent may report *"Python was not found; run without arguments to
install from the Microsoft Store"*. Close the opencode window and follow
[Before the exercises: check that Python is installed](00-prerequisites.md).
Then start the opencode app again. If the project under the text box is not
`kisd-pendulum`, click it and choose `kisd-pendulum` from the list. Repeat this
step.

</details>

## Step 3: Create the data file (`Write`)

The data below was typed into Polish Excel and exported as CSV, so it uses
semicolons and decimal commas. Paste the whole prompt, including the data:

```text
Create a file named pendulum.csv with exactly the content below.
It comes from a student lab: each row is one pendulum,
with the string length in metres
and the time of 10 full swings in seconds, measured with a stopwatch.
Do not change any values.

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

Expected result: *Used 1 Write*. Open it: you see the content of
`pendulum.csv`, 13 lines in green (*+13 -0*: 13 lines added, none removed).

<details>
<summary>Pictures: the Write in the test run</summary>

<img src="images/02-pendulum-3a-write-thought.png" alt="The Thought before the Write: the model notices that 70 would be absurd in metres, asks itself whether this is a trap, and decides to copy the data exactly and flag the row" width="700">

<img src="images/02-pendulum-3b-write-answer.png" alt="The new file pendulum.csv with 13 green lines, and the answer: the file was created unchanged, and the row 70;16,8 is flagged as a likely typo for 0,70" width="700">

</details>

**What to notice:** the agent created a real file on your disk. In a chat you
would have to copy the text and save the file yourself.

The agent probably already points at the row `70;16,8`. In its Thought it even
asks itself *"is it a trap?"* Because you said *do not change any values*, it
copied the row as it is and only told you. That is the right behaviour: you
decide what to do with raw data. Keep the row; it matters in step 5.

### Look at the file in opencode

opencode can show you the files of the project, next to the conversation.

1. At the top right of the window, click the **Toggle review** button (the
   square with a bar on the right), or press **Ctrl+Shift+R**.

   <img src="images/02-pendulum-3c-review-button.png" alt="The top bar of opencode; at the right, the Toggle review button with the tooltip Toggle review Ctrl Shift R" width="700">

2. A panel opens on the right with a **New tab**. Click **Open file** (or press
   **Ctrl+Alt+P**).

   ![The New tab in the right panel: Review, Browser, Terminal, and Open file](images/02-pendulum-3d-new-tab.png)

3. You see the files of your project. Click `pendulum.csv`.

   ![Open file: the project name and the list of files, with pendulum.csv](images/02-pendulum-3e-open-file.png)

Expected result: the file is shown as a table, but the columns look wrong:
`0` in one column, `20;8` in the next, `9` in the third.

![pendulum.csv shown as a table with wrongly split columns, such as 0 | 20;8 | 9](images/02-pendulum-3f-csv-table.png)

The file is fine; the viewer is not. It expects commas between the columns, as
in English CSV files, so it splits the Polish decimal commas instead of the
semicolons. Your own analysis must not make the same mistake. Watch whether the
agent's script does in step 4.

You can also open the file in File Explorer: go to your `kisd-pendulum` folder
and open `pendulum.csv` in Notepad.

## Step 4: Analyse (`Write` + `Shell` + the loop)

```text
Write a short Python script named analyse.py that reads pendulum.csv.
For each row, compute the period T (time of 10 swings divided by 10)
and g = 4 * pi^2 * length / T^2.
Print a table with one row per pendulum, then the mean of g.
Use only the Python standard library.
Run the script and show me the output.
```

Expected result: *Used 2 Write, Shell*: the new script `analyse.py` in green,
then **Shell** `python analyse.py`. Below it, the output: a table of 12 rows,
then a mean *g* of about **90.58 m/s²**. That is far too large. Keep it for the
next step.

<details>
<summary>Pictures: the script and its output in the test run</summary>

<img src="images/02-pendulum-4a-write.png" alt="The Thought: the model plans to split on semicolons, replace decimal commas, and leave the row 70 as it is; then the new file analyse.py, 33 lines" width="700">

<img src="images/02-pendulum-4b-output.png" alt="The rest of analyse.py, Shell python analyse.py, and the output: 12 rows with g near 9.8 except 979.1274 for the row 70.00, and Mean g = 90.5812; below, a note that without that row the mean would be about 9.78" width="700">

</details>

**What to notice:** several tool calls in a row: **Write** for the script, then
**Shell** to run `python analyse.py`. The agent read the output before answering
you. Its Thought shows that it saw the decimal commas in advance.

Sometimes the first run fails, because Python does not read `0,20` as a number.
You then see an error such as
`ValueError: could not convert string to float: '0,20'`, followed by an **Edit**
of the script and a second run, without you doing anything. That is the agentic
loop: act, look at the result, fix, try again. In our test runs the first run
always worked. Ask your neighbour whether theirs failed.

**Check the agent's words, not only its numbers.** In the test run the agent
wrote that without the row `70` *"the mean would be about 9.78 m/s²"*. The
correct value is **9.80**: you can see it yourself, all the other rows are
between 9.64 and 9.99. The agent did not run any code for that sentence; it
estimated it, and wrote the estimate as confidently as the real results. In the
same answer it also suggested correcting the CSV file. Do not do that yet.

## Step 5: Find the problem (reasoning on results)

```text
The mean g is far from 9.81 m/s^2.
Look at the per-row results and find out why.
Do not change any files yet, just explain.
```

Expected result: *Used 1 Thought*, then an explanation. The agent points to the
row with length `70`: its *g* is about **979**, a hundred times too large, and
this one row pulls the mean up from about 9.8 to 90.6.

<details>
<summary>Picture: the explanation in the test run</summary>

<img src="images/02-pendulum-5-explain.png" alt="The Thought adds up the eleven other values by hand and gets 9.8043; the answer: the row 70;16,8 gives 979, with 0.70 m it would give 9.79, and without it the mean is 9.80" width="700">

</details>

**What to notice:**

- The agent used the output of its own script as evidence. Here it did not need
  any tool; it reasoned on what it already had.
- In the Thought it added up the other eleven values by hand and got 9.80. So
  it quietly corrected its own *9.78* from step 4, without telling you.
- The best evidence is in the second column. The time, 16.8 s for 10 swings,
  gives T = 1.68 s, which is just what a **0.70 m** pendulum swings
  (g = 4π²·0.70/1.68² ≈ 9.79). So the length is wrong, not the time.
- The agent also says *why* the length is 70: in our test runs it always guessed
  a typo, a lost `0,`. A length in centimetres gives the same 0.70 m. The data
  cannot tell which; it is a guess, even if the agent sounds sure.

<details>
<summary>Explain: could it really be a 70 m pendulum?</summary>

A 70 m pendulum has a period of 16.8 s. That is exactly the number in the second
column. So the row could also mean "70 m, timed for **one** swing instead of
10". Only the setting rules this out: a student lab has no 70 m pendulum. The
numbers alone fit both stories. Context is your job, not the agent's.

</details>

## Step 6: Fix it (`Edit` or `Write`)

The agent found **which** value is wrong. It cannot know **why**: a lost `0,`
and a length in centimetres both give 0.70 m. That needs knowledge from outside
the data. Suppose you checked the student's lab notebook: they measured that
pendulum as 70 **cm** and typed the number without converting it. You give the
agent this fact, and you decide how to fix it:

```text
I checked the lab notebook.
That length of 70 is not a typo for 0,70:
the student measured 70 cm and forgot to convert it to metres.
Fix it in analyse.py, not in pendulum.csv:
convert any length above 10 to metres and print a warning when you do.
Also print the standard error of the mean of g.
Run the script again.
```

Expected result: a change of `analyse.py`, then **Shell** runs it again. The
output starts with a warning about the length 70, the row now shows `0.70` with
g = 9.79, and at the end a mean *g* of about **9.80 m/s²** with a standard error
of about **0.03 m/s²**.

<details>
<summary>Pictures: the fix in the test run</summary>

<img src="images/02-pendulum-6a-thought.png" alt="The Thought: the model plans the conversion of lengths above 10, a warning, and the standard error with the statistics module" width="700">

<img src="images/02-pendulum-6b-write.png" alt="The Thought ends with Let me write the whole file; then a Write of analyse.py, 43 lines, all green" width="700">

<img src="images/02-pendulum-6c-output.png" alt="Shell python analyse.py: Warning: length 70 looks like centimetres; converting to 0.7 m; the table with 0.70 and 9.7913; Mean g = 9.8032; Standard error of the mean = 0.0311; and a list of what changed" width="700">

</details>

**What to notice:** to change a file, the agent can **Edit** part of it (red and
green lines) or **Write** the whole file again (all green). In the test run it
chose to write the whole file again (*Used 2 Write, Shell*, *+43 -0*). Both
work, but with a full rewrite you cannot see at a glance what changed. The agent
then listed its changes in the answer; read that list. It also checked its work
by running the script again.

The final sentence of the test run says the result *"is consistent with the
accepted 9.81 m/s² within about one standard error"*. Check it: 9.81 − 9.803 =
0.007, and one standard error is 0.031. So it is true, with room to spare.

<details>
<summary>Explain: why fix the script and not the data?</summary>

`pendulum.csv` is the raw data. If you correct it by hand, nobody can later see
that a correction was made. The correction in the script is written down, prints
a warning, and runs the same way every time. This is good practice in any data
analysis, with or without an agent.

</details>

## Step 7: A separate Python for this project (`Shell` + installing a package)

For the plot the agent needs **matplotlib**, a Python package that is not part
of Python itself. You want it installed **inside this project folder**, not into
the Python of your whole computer. Python calls such a private copy a **virtual
environment** (venv for short).

```text
Create a Python virtual environment named .venv in this folder.
Install matplotlib into it.
From now on, in this session, run all Python code
with .venv\Scripts\python.exe, never with the system Python,
and install every package you need into .venv.
Do not activate the environment; call .venv\Scripts\python.exe directly.
```

Expected result: several **Shell** calls: `python -m venv .venv` to create the
environment, then `.venv\Scripts\python.exe -m pip install matplotlib`. The
installation takes up to a minute. opencode may ask you to allow the commands;
allow them. A new folder `.venv` appears in your project folder.

<details>
<summary>Pictures: the venv in the test run</summary>

<img src="images/02-pendulum-7a-venv.png" alt="Used 4 Shell, opened: python -m venv .venv; .venv\Scripts\python.exe --version gives Python 3.15.0; .venv\Scripts\python.exe -m pip install matplotlib with its download log; and an import check that prints matplotlib 3.11.2" width="700">

<img src="images/02-pendulum-7b-answer.png" alt="The answer: the venv was created, matplotlib 3.11.2 installed with numpy, pillow and fonttools, and from now on the agent will use .venv\Scripts\python.exe" width="700">

</details>

**What to notice:** the agent prepared the environment, not only the code. In
the test run it used four **Shell** calls, and two of them were checks you did
not ask for: the Python version inside the venv, and a test that matplotlib
really imports. Then it repeated your rule back to you. matplotlib brought other
packages with it, such as **numpy**; they are all inside `.venv`.

<details>
<summary>Explain: why a virtual environment?</summary>

- **Clean and separate.** Every project keeps its own packages, in its own
  versions. Installing something for one project cannot break another one.
- **Easy to remove.** Everything is in the `.venv` folder. Delete the folder and
  it is gone; your computer's Python stays as it was.
- **Repeatable.** You (or the agent) can list exactly what this project uses,
  and set it up again on another computer.
- **Why not activate it?** "Activating" a venv only changes the current
  terminal window. The agent opens a fresh one for each command, so the
  activation would be lost. On Windows, activation in PowerShell is also often
  blocked by the *running scripts is disabled on this system* rule. Calling
  `.venv\Scripts\python.exe` directly always works.
- **"From now on, in this session".** The agent remembers this only in the
  current conversation. In a new session, say it again, or write it into
  `AGENTS.md` (see [`/init`](../01-getting-started/05-first-steps.md)), which
  opencode reads at the start of every session.

On Linux and macOS the path is `.venv/bin/python` instead of
`.venv\Scripts\python.exe`; change the prompt accordingly.

</details>

## Step 8: Plot (`Edit` + `Shell` + `Read`)

```text
Add a plot to analyse.py:
the measured period T against the length as points,
and the curve T = 2*pi*sqrt(L/g) with the mean g as a line.
Label the axes with units. Save it as pendulum.png.
Do not open a plot window.
Run the script with the Python from .venv.
```

Expected result: an **Edit** of `analyse.py`, then **Shell**
`.venv\Scripts\python.exe analyse.py`. A new file `pendulum.png` appears in the
folder. The agent may then **Read** `pendulum.png`: the plot appears right in
the conversation. The 12 points lie on, or very close to, the red curve.

<details>
<summary>Pictures: the plot in the test run</summary>

<img src="images/02-pendulum-8a-edit.png" alt="Used 4 Edit, Shell, Read, opened: the Thought plans a file-only matplotlib backend, Agg, and numpy for the curve; the Edit adds the matplotlib imports" width="700">

<img src="images/02-pendulum-8b-run-read.png" alt="The rest of the Edit with the plotting code; Shell .venv\Scripts\python.exe analyse.py with the table; a Thought: Let me view the png to confirm it looks right; and Read pendulum.png" width="700">

<img src="images/02-pendulum-8c-plot.png" alt="The plot shown in the conversation: blue measured points on a red curve T = 2π√(L/g) with g = 9.803; then the agent describes the plot" width="700">

</details>

**What to notice:**

- Check the **Shell** line. It should say `.venv\Scripts\python.exe
  analyse.py`. If it says plain `python analyse.py`, the agent forgot the venv
  and the script fails with *No module named 'matplotlib'*. Remind it: "Use
  .venv\Scripts\python.exe."
- To keep the plot window closed, the agent switched matplotlib to its
  file-only mode (`matplotlib.use("Agg")`).
- After saving the plot, the agent **read the picture** to check its own work,
  without being asked. See below what that means.

<details>
<summary>Explain: what does it mean that the agent "reads" a png file?</summary>

**Read** works on pictures too. When the agent reads `pendulum.png`, opencode
does two things:

- It shows the picture to **you**, in the conversation. That is why the plot
  appeared without you opening it.
- It sends the picture to the **model** as an image, so that a model that can
  understand pictures can look at it, as a person looks at a screenshot.

Whether the model really *looked* is another matter. Some models read only
text; for them the picture is just a note that a file was read. The agent's
description in the test run (blue points, red curve, labels with units) matches
what the code was told to draw, so it could have been written without seeing
the picture at all. You cannot tell from the answer.

So: when the agent says "the plot looks right", treat it as a claim, not a
check. Look at the plot yourself. Things only your eyes may catch: a point far
off the curve, an axis in the wrong units, a curve that does not pass through
the points.

</details>

<details>
<summary>Explain: why "do not open a plot window"?</summary>

A plot window stays open until you close it, and the script waits for it. The
agent would wait too, and look frozen. Saving to a file lets the script finish.

</details>

## Step 9: Look at the plot

The plot is a file in your project. Ask the agent to show it to you:

```text
Show me pendulum.png.
```

Expected result: the plot opens in the panel on the right, and again in the
conversation. In the test run the agent used two new tools: **Skill** (it loaded
instructions for the opencode browser) and **Execute** (it ran
`browser.preview` with the file `pendulum.png`).

<details>
<summary>Picture: the plot in the side panel in the test run</summary>

<img src="images/02-pendulum-9-show.png" alt="Used 2 Skill, Execute: a long Thought weighing a second Read against the browser preview; Loaded Browser skill; Execute browser.preview with path pendulum.png; the plot in the conversation and in the right panel, pendulum.png" width="700">

</details>

**What to notice:** open the Thought. The model spends a long paragraph going
back and forth: read the picture again, or use the browser preview? It changes
its mind several times. A simple request can cost many model calls, and so
credits. The plot was already visible after step 8; this step was not needed,
but now you know where to find it.

In the right panel, the zoom buttons at the bottom right make the plot larger.
You can also open `pendulum.png` from File Explorer, in your `kisd-pendulum`
folder.


## Step 10: Write the report (`Write` from results)

```text
Write report.md: a half-page lab report with
the aim, the method (the formula),
a note about the centimetre correction,
the result for g with its standard error,
a comparison with 9.81 m/s^2,
and the plot pendulum.png embedded.
```

Expected result: *Used 2 Write, Execute*: a **Write** of `report.md`, then
**Execute** `browser.preview`, which opens the report in the panel on the right.
There you see it formatted: headings, the formulas, and the plot. You do not
need any other program to read it.

<details>
<summary>Picture: the report in the test run</summary>

<img src="images/02-pendulum-10-report.png" alt="Left: Used 2 Write, Execute, with report.md (+54 lines) and browser.preview report.md, then a summary of the report. Right: report.md formatted, with the sections Aim, Method with the formulas, and Centimetre correction" width="700">

</details>

**What to notice:** the numbers in the report should match what the script
printed in step 6: g = 9.803 ± 0.031 m/s². **Check this yourself.** In the test
run they matched, and the comparison was right: 0.007 m/s² from 9.81, about 0.2
standard errors.

Now read the text, not only the numbers. The report in the test run says that
*"the length L was measured with a ruler"*. Nobody said that; the agent made it
up because it sounds like a normal lab report. It is a small detail, but it is
not true to what you know. In your own reports, every sentence the agent writes
is a sentence you sign.

## Step 11: Ask the agent what it did

```text
List, in order, every tool you used in this session,
and for each one say in one sentence why you needed it.
```

Expected result: *Used 1 Thought*, then a numbered list of about 20 tool calls,
each with a reason. Compare it with the *Used ...* lines you saw on screen. Do
they match?

<details>
<summary>Pictures: the list in the test run</summary>

<img src="images/02-pendulum-11a-tools-thought.png" alt="The Thought: the model rebuilds the session in order, 19 tool calls from read of the folder to execute browser.preview report.md" width="700">

<img src="images/02-pendulum-11b-tools-list.png" alt="The answer: 19 tool calls in order, each with one sentence why: read, shell, write, write, shell, write, shell, four shell calls for the venv, edit, edit, shell, read, skill, execute, write, execute" width="700">

</details>

**What to notice:** in the test run the list had 19 tool calls, and it matched
the screen exactly: the counts in the *Used ...* lines add up to 19 too. The
agent did not need any tool for this; it reads the whole conversation every
time, so it knows what it did. Most of the calls were **Shell**: running code
and checking things is most of the work.

## Step 12: How big was this session?

opencode counts the size of the session. At the top of the conversation, next
to the session name, is a small circle with a number, for example **22k**. Click
it (the tooltip says *Toggle session context*). A **Context** tab opens in the
right panel.

<img src="images/02-pendulum-12-context.png" alt="The Context tab: Session Simple pendulum period and g; Provider PLGrid Forge (Cyfronet); Model DeepSeek V4.1 Flash (PLGrid); Messages 51; Context Limit 1,048,576; Total Tokens 22,386; Usage 2%; Input Tokens 21,620; Output Tokens 448; Reasoning Tokens 318; User Messages 11; Assistant Messages 29; Total Cost $0.00" width="700">

What the numbers mean (from the test run):

- **User Messages 11**, **Assistant Messages 29**: your 11 prompts led to 29
  answers of the model. One prompt can take several model calls.
- **Total Tokens 22,386** and **Usage 2%**: the size of the last model call. A
  **token** is a piece of a word, about ¾ of an English word. The model can hold
  1,048,576 tokens (**Context Limit**), so this session fills 2% of it.
- **Input Tokens 21,620**, but **Output Tokens 448** (plus 318 for the
  Thought). With every call, the whole conversation so far is sent to the model
  again; the model writes only a little each time. That is why each prompt in
  a long session costs more than the one before.
- **Total Cost $0.00**: opencode does not know the prices of PLGrid. The real
  cost is counted in **credits** on llmlab. Look at your usage on the llmlab
  page.

See also [why one prompt costs many model calls](../01-getting-started/01-chatbot-or-agent.md#why-one-prompt-costs-many-model-calls).

<details>
<summary>Explain: start a new session for a new task</summary>

Because the whole conversation is sent again with every call, a session that
goes on and on gets slower and costs more credits per prompt. For a new task,
start a new session (the **+** at the top, or **Ctrl+T**). Things the agent
should always know, such as "use .venv", go into `AGENTS.md` (see
[`/init`](../01-getting-started/05-first-steps.md)), not into a long
conversation.

</details>

You are done. Close the opencode window, or keep it open for "Going further".

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
| Leaves results | Text in a chat window | Files on your disk: `analyse.py`, `pendulum.png`, `report.md`, and the `.venv` with its packages |
| Your job | Copy, paste, run | Give clear tasks, **check the results** |

The last row matters most. The agent can be confidently wrong. It found the
centimetre error here because the number was absurd. A small error, say 0,60
typed as 0,61, would pass silently. You are still the scientist.

## If something goes wrong

- **The agent only describes what it would do but does not do it.** Reply
  "Go ahead and do it."
- **"not available for grant"**: your grant cannot use the default model. See
  [Which model](../01-getting-started/05-first-steps.md#which-model).
- **`pip` fails with a network or proxy error.** Ask the agent to try again; if it
  keeps failing, skip the plot and do step 10 without it.
- **The agent stops without a result.** Check whether the expected file exists.
  If not, send the same prompt again, or add "Run the script and show me the
  output" at the end.
- **Activate.ps1 cannot be loaded because running scripts is disabled.** The
  agent tried to activate the venv. Reply "Do not activate; call
  .venv\Scripts\python.exe directly."
- **No module named 'matplotlib'.** The agent ran the system Python. Reply "Use
  .venv\Scripts\python.exe."
- **The numbers differ a little from those above.** Small differences (for example
  9.79 instead of 9.80) are fine. A different order of magnitude is not: ask the
  agent to explain.

<details>
<summary>Going further</summary>

- **Change the data yourself.** Open `pendulum.csv` in Notepad, add a row
  `1,40;23,8`, save, and ask the agent to "run the analysis again". It reads the
  current file, not what it remembers.
- **Plan first.** Ask: "Do not change any files yet. How would you estimate g
  from a straight-line fit of T^2 against L instead of the mean?" The agent only
  reads and proposes. If the plan looks good, reply "Go ahead" and let it do it.
  Does the fit give the same *g*? Why might it not?
- **Undo.** `/undo` takes back the last message and its file changes. It needs a
  Git repository: ask the agent to "initialise a git repository and commit all
  files" first.
- **Look at the code.** Open `analyse.py`. It is about 40–80 lines. Ask the
  agent to explain any line you do not understand.

</details>

<details>
<summary>For experts: what a session looks like inside</summary>

opencode keeps every message of a session as a small JSON record. You can see
them in the **Context** tab of step 12, under **Raw messages**; **Export
session** saves the whole session to a file. The pictures below come from the
test run.

**Your prompt** (picture 1) is a record with `"type": "user"`:

- `text` is your prompt exactly as typed. The line breaks of the copy-paste
  blocks arrive as `\n`, plain new lines; they do not change the meaning.
- `metadata.agent` is `"build"`: the opencode agent that may change files. (The
  terminal version of opencode also has a read-only `plan` agent.)
- `metadata.model` names the provider `"plgrid"` and the model
  `"deepseek-ai/DeepSeek-V4.1-Flash"`. Both come from the settings file of
  [part 1](../01-getting-started/03-configure-llmlab-endpoint.md): `plgrid` is
  the name of the provider in
  [opencode.json](../01-getting-started/config/opencode.json), the model name is
  what llmlab calls it.
- `time.created` is a Unix time in milliseconds: 1793636270586 is
  2 November 2026, 17:17:50.

**The answer** (picture 2) is a record with `"type": "assistant"`. Its
`content` is a list of parts:

- A part `"type": "reasoning"`: this is the **Thought** you saw in the app. Its
  `native.reasoningField` is `"reasoning"`: llmlab sends the model's thinking in
  a separate field of the answer with that name, and the line
  `"interleaved": {"field": "reasoning"}` in `opencode.json` tells opencode where
  to find it. Its `time` shows the thinking took 1.3 s, the *Thought 1s* in the
  app.
- A part `"type": "text"`: the answer itself.
- In steps that use tools, the tool calls and their results are further parts
  of the same kind of list.
- `model.variant` is `"default"`: the **Default** shown next to the model name
  under the text box. Some models offer variants, for example more or less
  thinking; DeepSeek here runs in its default one.

**The "Show me" prompt of step 9** (picture 3): the reasoning part holds the
same long, hesitating text you saw as the Thought. The app shows the model's
thinking as it is, not a cleaned-up summary. That is useful: when an agent does
something strange, its reasoning often tells you why.

Why this matters for research: the raw records are an exact log of what you
asked, which model answered, when, and what it thought and did. If you use an
agent for real analysis, export the session and keep it next to your results,
as you would keep a lab notebook.

<details>
<summary>Picture 1: a user message</summary>

<img src="images/02-pendulum-raw-1-user.png" alt="Raw messages: the JSON of the first prompt, with id, type user, text with \n line breaks, metadata with agent build and model deepseek-ai/DeepSeek-V4.1-Flash from provider plgrid, and time created" width="700">

</details>

<details>
<summary>Picture 2: an assistant message</summary>

<img src="images/02-pendulum-raw-2-assistant.png" alt="The JSON of the first answer: type assistant, agent build, model with variant default, and content with a reasoning part (native reasoningField reasoning, created and completed times) and a text part with the answer" width="700">

</details>

<details>
<summary>Picture 3: the "Show me pendulum.png." messages</summary>

<img src="images/02-pendulum-raw-3-show-me.png" alt="The JSON of the prompt Show me pendulum.png. and the start of the answer, whose reasoning part holds the long text about read versus browser.preview" width="700">

</details>

</details>

---

← Previous: [Create a project folder](01-project-folder.md) · [Exercises overview](README.md) · Next: [Learning with an agent →](03-learning-with-an-agent.md)
