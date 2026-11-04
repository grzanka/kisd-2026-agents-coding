# Exercise 2: Twenty years of weather in Katowice

The weather station of GIG (Główny Instytut Górnictwa) in Katowice has measured
temperature, humidity, wind, rain and more every 30 minutes since 2006. The
archive is public: about 1,000 text files, 100 MB in total. That is too much to
look through by hand, and too much for the model to read. The agent has to work
**through code**.

The data is real, so it is messy. A simple "combine all files" gives wrong
results **without any error message**. Finding out why is the main part of this
exercise.

This time the agent works in **learning mode**: it explains what it does, and
you predict, check and change things yourself. Read
[Learning with an agent](03-learning-with-an-agent.md) first.

- Time: about 100 minutes, in three parts. Each part is a new session.
- You need: everything from [Exercise 1](02-pendulum.md), and about 300 MB of
  free disk space.
- You learn: rules in `AGENTS.md`, a plan in a file, sessions and handover
  notes, `@` for files, checking data, CSV versus Parquet, and changing code
  yourself.

<details>
<summary>Explain: where the data comes from, and the licence</summary>

The data is published at <https://meteo.gig.eu/archiwum/> under the licence
**CC BY 4.0**: you may use it for anything, if you name the source. In every
report and plot, write: *Data: meteo.gig.eu archive, GIG Katowice, CC BY 4.0*.
The agent does this for you, because the rules in `AGENTS.md` say so.

So that 30 people do not download 1,000 files each from a small institute's
server at the same time, we keep an unchanged copy of the whole archive as one
ZIP file (16 MB) in this course's
[GitHub release](https://github.com/grzanka/kisd-2026-agents-coding/releases/tag/data-gig-meteo-2006-2026).

</details>

## Part A: rules and plan (session 1, about 15 minutes)

### A1. Create the project folder

Create the folder `kisd-meteo` and open it in opencode, as in
[Create a project folder](01-project-folder.md).

### A2. Get the rules file

Paste this prompt. The long address must stay on one line:

```text
Download this file into the project folder as AGENTS.md:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/meteo/AGENTS.md
Then show me its content. Do nothing else.
```

Expected result: a **Shell** or **Webfetch** call, then the rules of the
project. Read them: this is what the agent will follow from now on.

<!-- TODO: screenshot of the download of AGENTS.md. -->

### A3. Start a new session, and test the rules

opencode reads `AGENTS.md` only when a session **starts**. The current session
started before the file existed, so it does not know the rules yet. Start a new
session: click the **+** at the top, or press **Ctrl+T**. Then:

```text
What rules do you follow in this project?
```

Expected result: the agent lists the rules in its own words: explain before
acting, ask check questions, use `.venv`, never change `data/raw`, cite the
source.

If it answers with general rules and does not mention `data/raw` or the source,
it has not read the file. Check that `AGENTS.md` is in the project folder (in
the right panel, **Open file**), and start another new session.

### A4. Plan first, in a file

```text
We will analyse the weather data of the GIG station in Katowice, 2006-2026.
The whole archive is one ZIP file:
https://github.com/grzanka/kisd-2026-agents-coding/releases/download/data-gig-meteo-2006-2026/gig-meteo-2006-2026.zip
Plan how to download it, unpack it into data/raw,
check the files, and combine them into one clean table.
Do not download or run anything yet.
Write the plan to PLAN.md: numbered steps, and for each step
what it produces and how we will check that it worked.
```

Expected result: a **Write** of `PLAN.md` with about 5–10 steps. Open it in
the right panel (**Open file**, then `PLAN.md`) and read it.

<!-- TODO: screenshot of PLAN.md. -->

### A5. Question the plan, then change it

Ask about the plan:

```text
Which step of your plan is the riskiest, and why?
```

Then change one thing in the plan yourself. Write your own request, or use
this one:

```text
Add a step before combining: check that the dates in every file
are plausible and match the date in the file name.
Update PLAN.md.
```

Most agents name the step that combines the files: a mistake there gives a
table that looks fine and is wrong.

**What to notice:** the plan is a file. You can read it, question it and change
it **before** anything happens. That is cheaper than fixing things afterwards.
The plan also survives the end of the session.

### A6. Write the handover

```text
Write NOTES.md: what we decided so far,
and what the next session must know to continue.
```

Expected result: a short `NOTES.md`. Read it. Would a colleague understand
where you stopped?

This session is done.

## Part B: from 1,000 text files to one fast file (session 2, about 50 minutes)

Start a **new session** (**+** or **Ctrl+T**). It knows nothing about part A,
except what is in the files.

### B1. Continue from the files

```text
Read PLAN.md and NOTES.md.
Tell me in two sentences where we are and what comes next.
```

Expected result: the agent reads both files (**Read**) and sums up. It knows
where to continue, although it is a new session: the knowledge was in the files,
not in its memory.

### B2. Predict, then download

Before you send the prompt, write down your guess: **how many files are in the
archive, and how many MB once unpacked?**

```text
Carry out the download and unpacking steps of PLAN.md only.
Then stop and report: number of files, total size,
the folders you found, and anything unexpected.
```

Expected result: the agent first does the earlier steps of the plan, if there
are any (folders, `.venv`). Then it downloads the ZIP (16 MB) and unpacks it,
often with small scripts that it writes first (**Write**, then **Shell**). It
reports **1,066 files** in 21 year folders (`2006` to `2026`), about **100 MB**,
and a `README.txt`. The files are in `data/raw/gig-meteo/`, one folder deeper
than you might expect. Compare with your guess.

The archive comes with a short `README.txt`, and a careful agent reads it. It
lists some of the problems in the data, and says itself that the list may not
be complete. In step B4 you will see what it leaves out.

<!-- TODO: screenshot of the download and unpacking. -->

### B3. Look at one file with @

In the text box, type `@` and then `20150105`. A list of matching files
appears. The same file is in two folders, `2014` and `2015`: the week of New
Year belongs to both years. Choose `data/raw/gig-meteo/2015/20150105.txt`. Then
paste the rest of the prompt after it:

```text
What does each column mean? Which columns are you unsure about?
Are there units anywhere in the file?
```

The file is attached only if you **choose it from the list**. If you type or
paste the path yourself, it stays plain text, and the agent opens the file with
a **Read** call instead. That works too, but it is not `@`.

Expected result: the agent explains the columns. It may recognise the format: a
Davis weather station, exported with the program WeatherLink. It should notice
that:

- the column names are split over **two lines** (for example `Temp` above
  `Out`);
- the first date is `14-12-28`, which means 28 December 2014: the file named
  after 5 January 2015 starts 8 days earlier;
- **there are no units in the file.** The values look like °C, %, hPa and mm,
  but that is an assumption.

If the agent states the units as facts, ask it: "How do you know?"

<!-- TODO: screenshot of the @ list and the answer. -->

Then ask:

```text
Why should I never use @ on the combined file of all years?
```

<details>
<summary>Explain: @, and why big files must not go into the conversation</summary>

`@` pastes the **whole content** of the file into your prompt. One weekly file
is about 100 KB: fine. The whole archive is 100 MB, about 30 million tokens.
The model can hold about 1 million, so it would not even fit. And remember: the
whole session is sent again with every prompt.

So with real data, the agent never "reads" the data. It writes code that reads
the data, and looks only at the summaries that the code prints. That is also
why its answers about data are only as good as its code. Check the code's
results, not the agent's impression.

</details>

### B4. Check before combining

```text
Carry out the checking steps of PLAN.md.
Do all files have the same columns? Are all dates plausible,
and do they match the dates in the file names?
Are there lines that are not data?
Report what you find; do not fix anything yet.
```

Expected result: several **Write** and **Shell** calls with small checking
scripts, then a report. This is the most important step of the exercise. Take
your time to read the report.

Wait for the report before you send the next prompt. If the agent stops after
writing a script, without running it, write: "Run it and report."

<details>
<summary>Check: what the agent should find (open after you read its report)</summary>

The archive has these problems. How many did your agent find?

1. **Weeks overlap.** Each file covers 8 days, so two neighbouring files share
   one day. Simply joining all files counts about 58,000 half-hours twice.
2. **Files at New Year are there twice.** For example `20070101.txt` is in both
   `2006/` and `2007/`. There are 16 such files.
3. **The date format changes.** In 83 files, from March 2009 to November 2010,
   the date is written day-month-year: `5-04-09` means 5 April 2009, not
   9 April 2005. Read as year-month-day, such dates become nonsense, up to the
   year **2031**.
4. **Notes between the data rows**, in Polish, starting with `****`: clock
   changes and server outages.
5. **Missing values** written as `---`.
6. **Values that touch.** The files are fixed-width: each value has its own
   columns of characters. A value that is too wide touches its neighbour:
   `992.4108.801280.0` is really `992.4`, `108.80` and `1280.0`. Split at the
   spaces, such a row seems to have values missing. There is also one row cut
   short, and one line with two rows glued together.
7. **Two column layouts.** Some files, for example `2018/20180604.txt`, have
   wider columns than the rest.
The `README.txt` mentions problems 1 to 5, but without the details: it does not
say which files use day-month-year dates, or how many. Problems 6 and 7 are not
in it at all. Read the documentation, then check it against the data.

Problems 1, 2 and 3 do not cause any error message. A script that ignores them
runs fine and gives wrong numbers.

</details>

### B5. Combine, and explain the code

```text
Now combine all files into one table and save it as data/meteo.csv.
Handle every problem you found in the previous step, and say how.
Explain the 3 most important lines of the code that reads the files.
```

Before the agent finishes, predict: **how many rows will the table have?** Hint:
there are 48 half-hours in a day, and about 20 years.

Expected result: this step takes longest, often 10–15 minutes. The agent writes
a script, runs it, finds new problems in its own output, writes small test
scripts, and changes its script several times. That is normal: watch how it
checks its own results. At the end you have a script, a new file
`data/meteo.csv` (about 55–60 MB), and a report with the number of rows: about
**349,000**.

Check your prediction: from 1 October 2006 to 2 November 2026 there are 7,337
days, and 7,337 × 48 = 352,176 half-hours. The table has about 3,000 fewer,
because the station did not always work. If your agent reports about 407,000
rows, it did not remove the overlapping days. If it reports far fewer than
349,000, it probably lost the rows with day-month-year dates.

Then ask one question of your own about the code. If you have no idea, ask:

```text
What would happen if one file had a column fewer than the others?
```

### B6. CSV versus Parquet

A CSV file is plain text: easy to open, slow and large. **Parquet** is a binary
format made for tables. Predict first: how much smaller will the Parquet file
be?

```text
Convert data/meteo.csv to Parquet, as data/meteo.parquet.
Then measure and show me a table:
file size, time to read the whole file,
and time to read only the timestamp and Out Hum columns,
for both CSV and Parquet.
Explain what Parquet does differently.
```

Expected result: the agent installs `pyarrow` into `.venv` (`pandas` is
usually there already), then shows a table like this (the times depend on your
computer):

| | CSV | Parquet |
|---|---|---|
| Size | about 55 MB | about 10 MB |
| Read everything | about 0.4 s | about 0.03 s |
| Read 2 columns | about 0.2 s | about 0.015 s |

Look at the CSV column: reading 2 columns instead of all saves only about half
the time, because the program still has to go through every line of text.

<details>
<summary>Explain: why Parquet is smaller and faster</summary>

- **By columns.** A CSV file stores the data row by row. Parquet stores it
  column by column. To read 2 of 35 columns, Parquet reads only those 2.
- **Compressed.** Values in one column are similar (temperatures next to
  temperatures), so they compress well.
- **Typed.** Parquet knows that a column holds dates or numbers. A CSV file holds
  only text, which has to be converted again every time it is read.
- **Partial reads.** A Parquet file can be split into parts, for example by
  year. A program that needs only 2015 then reads only that part.

Other formats with similar ideas: Feather, HDF5, and for very large data,
databases such as DuckDB.

</details>

**What to notice:** `data/raw` is still untouched. `meteo.csv` and
`meteo.parquet` are **derived** files: you can delete them and make them again
from the raw files with the script. That is what makes the analysis
reproducible.

### B7. Write the handover

```text
Update NOTES.md: where the data is, how many rows,
which problems we found and how we handled them,
and that the analysis should use only data/meteo.parquet.
```

Before you leave, click the small circle with the token count next to the
session name, as in [Exercise 1, step 12](02-pendulum.md#step-12-how-big-was-this-session).
In our test run, this session ended at about 180,000 tokens: each of the last
prompts sent that much to the model again. Over the whole session, the model
read about 5 million tokens. That is why part C starts a new session.

This session is done.

## Part C: analysis (session 3, about 30 minutes)

Start a **new session**.

```text
Read NOTES.md. In this session, read the data only from data/meteo.parquet.
```

### C1. Questions before answers

```text
Before any analysis: list 5 questions we should ask about this data
to know whether we can trust it. Then check each one.
```

Expected result: questions such as: Are the temperatures in a plausible range?
Is the humidity between 0 and 100%? Is July warmer than January in every year?
Does any year have too few data? Does any sensor show the same value for days?

<details>
<summary>Check: what the agent might find</summary>

- A temperature of exactly **−67.8 °C**, about 140 times, between 2009 and
  2022: always the same value, so a sensor error code, not weather. The coldest
  real values in Katowice are around −25 °C.
- A **rain rate** of more than 1,000 mm per hour in a few rows: impossible. The
  world record is about 300 mm in one hour.
- **2009** and **2019** have a few percent fewer data than other years: each
  has a gap of about two weeks.
- The first and last years are incomplete: the archive starts in October 2006
  and ends in November 2026.

What to do with such values is **your** decision, not the agent's. Ask it to
list the options.

</details>

### C2. Humid hours: predict, then compute

Long periods of high humidity matter to plants and fungi: many plant disease
models count hours with relative humidity of 90% or more. Predict first: **in
which month are there most such hours? At what time of day is the air most
humid?**

```text
For each month, count the hours with relative humidity (Out Hum)
of 90% or more, averaged over the full years 2007-2025. Make a bar chart.
Also make a heatmap of the mean humidity by hour of day and month.
Save both in results/. Explain the code that counts the hours.
```

Expected result: the agent installs `matplotlib` into `.venv`, if it is not
there yet, then makes two plots in `results/`. November to January have the
most humid hours (about 360–410 per month), April to August the fewest (about
125–160). In the heatmap, the air is most humid around sunrise (about 88%) and driest in
the afternoon (about 68%).

<!-- TODO: screenshots of the two plots. -->

### C3. Change it yourself

```text
In which file and on which line is the 90% threshold set?
Do not change it. Just tell me.
```

Open that file in Notepad (in File Explorer, go to your `kisd-meteo` folder,
right-click the file, **Open with**, **Notepad**). Change `90` to `80`, and save.
Predict: more humid hours or fewer? Then:

```text
I changed the threshold myself. Run the script again,
and tell me what changed in the results.
```

Expected result: about 1.7 times more humid hours in every month, for example
about 630 instead of 407 in December. The heatmap does not change: it shows the
mean humidity and does not use the threshold.

**What to notice:** this is the first line of code you changed yourself. The
agent explained where; you did it. But look at the bar chart: its title still
says "90 %". The number 90 was written in more than one place, and you changed
only one of them. A good agent warns you about this. If yours does not, ask:
"Where else in the script does 90 appear?"

### C4. Is there a trend?

```text
Count the humid hours (90% or more) for each full year, 2007-2025,
and plot them. Is there a trend?
List the reasons why such a trend could be wrong.
```

Expected result: a plot and a list of reasons for caution: years with missing
data (2009 and 2019 each miss about two weeks), one station only, possible
sensor changes, and how the date problem of 2009-2010 was handled. The values
lie between about 2,100 and 3,700 hours a year, and they jump a lot from year
to year. A straight line through them is flat: there is no trend that the data
can show. If 2009 and 2010 show far fewer humid hours than the other years, ask
the agent why: the date problem was not handled.

### C5. Short report: let the agent ask you

A report needs decisions that are yours: which results to show, and what to do
with the chart you changed in C3. Instead of letting the agent guess, make it
ask you, with options to choose from:

```text
Write results/report.md: half a page with what we did, the problems we found
in the data and how we handled them, the two main results with their plots,
and the source of the data.
Before you write: ask me which two results to include, and anything else
you are not sure about. Use your question tool, with 2-3 options for each.
```

Expected result: no report yet. The agent stops and shows its questions, each
with 2–3 options and a short description of each option. For example: "Which
two results should the report feature?", with the options "Monthly bar +
humidity heatmap" and "Monthly bar + yearly trend". Click your choice for each
question. Only then does the agent write the report.

<!-- TODO: screenshot of the Question box with options. -->

Read the report. Is every sentence true? Is anything made up? Pick one claim
and ask: "How do you know that?" For example, a report may say that the empty
wind directions are moments without wind. Is that checked, or assumed?

<details>
<summary>Explain: the question tool</summary>

opencode gives the agent a tool called **question**. With it, the agent stops
and shows you questions with ready answers to click, instead of writing a
question in the chat. Agents use it by themselves when they notice that
something is unclear; with "use your question tool" you make sure that it does.

This is useful whenever a decision is yours: which data to leave out, which
plot to show, which threshold to use. Clicking an option is quicker than
writing an answer, and the options show you what the choices are, which you
might not have thought of.

The rules in `AGENTS.md` ask the agent to do this whenever it needs a decision
from you.

</details>

## Check yourself

No prompt here. Answer on paper, or explain to your neighbour:

1. Where does the data come from, and what licence does it have?
2. Name two problems in the raw files that cause **no error message**.
3. Why is reading two columns from Parquet so much faster than from CSV?
4. Name one thing the agent knew only because of `AGENTS.md`, and one only
   because of `NOTES.md`.
5. Which result would you trust least, and why?
6. What did you change yourself, and what happened?

## If something goes wrong

- **The agent does not follow the rules** (no explanations, no check
  questions). It did not read `AGENTS.md`: start a new session.
- **The download fails.** Ask the agent to try again. If the network blocks
  GitHub, download the ZIP in your browser from the
  [release page](https://github.com/grzanka/kisd-2026-agents-coding/releases/tag/data-gig-meteo-2006-2026),
  put it into your `kisd-meteo` folder, and tell the agent where it is.
- **`pip install pyarrow` fails.** Ask the agent to use `polars` instead of
  `pandas` and `pyarrow`. It also writes Parquet files.
- **The agent tries to read the whole CSV into the conversation.** Stop it
  (the stop button next to the text box) and remind it: "Do not read the data
  into the conversation; write code that summarises it."
- **The answers get slow, or the agent goes in circles.** Ask it to update
  `NOTES.md`, then start a new session.

<details>
<summary>Going further</summary>

- **Growing degree days.** For each year, add up the daily mean temperatures
  above 5 °C. Is the growing season getting warmer?
- **Frost.** The last spring frost and the first autumn frost of each year.
- **Partial reads.** Save the Parquet file split by year, and measure how fast
  one year can be read.
- **The original source.** Download one year directly from
  <https://meteo.gig.eu/archiwum/>, with a pause between files so that the
  server is not overloaded, and check that it is the same as in the ZIP.
- **Explore.** Ask `@explore` to look through your project and describe what
  each file does.

</details>

---

← Previous: [Learning with an agent](03-learning-with-an-agent.md) · [Exercises overview](README.md) · [Main README](../README.md)
