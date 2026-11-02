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

- Time: about 90 minutes, in three parts. Each part is a new session.
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
ZIP file (15 MB) in this course's
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
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/lecture-content-setup/02-exercises/meteo/AGENTS.md
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

## Part B: from 1,000 text files to one fast file (session 2, about 40 minutes)

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

Expected result: **Shell** calls that download the ZIP (15 MB) and unpack it.
The agent reports **1,066 files** in 21 folders (`2006` to `2026`), about
**100 MB**, and a `README.txt`. Compare with your guess.

<!-- TODO: screenshot of the download and unpacking. -->

### B3. Look at one file with @

In the text box, type `@` and then `2015010`. A list of matching files appears;
choose `data/raw/2015/20150105.txt`. Then add the rest of the prompt:

```text
@data/raw/2015/20150105.txt
What does each column mean? Which columns are you unsure about?
Are there units anywhere in the file?
```

Expected result: the agent explains the columns. It should notice that:

- the column names are split over **two lines** (for example `Temp` above
  `Out`);
- the dates look like `15-01-05`, which means 2015-01-05;
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

<details>
<summary>Check: what the agent should find (open after you read its report)</summary>

The archive has these problems. How many did your agent find?

1. **Weeks overlap.** Each file covers 8 days, so two neighbouring files share
   one day. Simply joining all files counts about 57,000 half-hours twice.
2. **One file is there twice:** `20070101.txt` is in both `2006/` and `2007/`.
3. **The date format changes.** In many files from 2009 and 2010 the date is
   written day-month-year: `5-04-09` means 5 April 2009, not 9 April 2005. Read
   as year-month-day, such dates become nonsense, up to the year **2031**.
4. **Notes between the data rows**, in Polish, starting with `****`: clock
   changes and server outages.
5. **Missing values** written as `---`.
6. **A few broken rows** with fewer or more values than the header, one of them
   two rows glued together.

Problems 1 and 3 do not cause any error message. A script that ignores them
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

Expected result: a script in the project folder, a new file `data/meteo.csv`
(about 60 MB), and a report with the number of rows: about **330,000–340,000**.
That is less than 20 years × 365 days × 48 = 350,400, because the station did
not always work.

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

Expected result: the agent installs `pandas` and `pyarrow` into `.venv`, then
shows a table like this (the times depend on your computer):

| | CSV | Parquet |
|---|---|---|
| Size | about 60 MB | about 10 MB |
| Read everything | about 0.3 s | about 0.2 s |
| Read 2 columns | about 0.4 s | about 0.03 s |

<details>
<summary>Explain: why Parquet is smaller and faster</summary>

- **By columns.** A CSV file stores the data row by row. Parquet stores it
  column by column. To read 2 of 36 columns, Parquet reads only those 2.
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

- One temperature of about **−68 °C**: a sensor error, not weather. The coldest
  real values in Katowice are around −25 °C.
- **2010** has far fewer data than other years: long gaps.
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
For each month, count the hours with relative humidity (Out Hum) of 90% or more,
averaged over the full years 2007-2025. Make a bar chart.
Also make a heatmap of the mean humidity by hour of day and month.
Save both in results/. Explain the code that counts the hours.
```

Expected result: two plots in `results/`. December and January have the most
humid hours (about 350–400 per month), April to August the fewest (about 120–150).
In the heatmap, the air is most humid around sunrise (about 88%) and driest in
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

**What to notice:** this is the first line of code you changed yourself. The
agent explained where; you did it.

### C4. Is there a trend?

```text
Count the humid hours (90% or more) for each full year, 2007-2025,
and plot them. Is there a trend?
List the reasons why such a trend could be wrong.
```

Expected result: a plot and a list of reasons for caution: years with missing
data (2010 has long gaps), one station only, possible sensor changes, and how
the date problem of 2009-2010 was handled. If 2009 and 2010 show far fewer humid
hours than the other years, ask the agent why.

### C5. Short report

```text
Write results/report.md: half a page with what we did, the problems we found
in the data and how we handled them, the two main results with their plots,
and the source of the data.
```

Read the report. Is every sentence true? Is anything made up?

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
