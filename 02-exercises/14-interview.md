# Exercise 12: the agent interviews you

Much of what a project needs is in nobody's files: why a measurement was
repeated, which column is in which unit, which samples are suspect. It is in
your head, or your supervisor's. An agent that starts on your data cannot know
it, and will guess. So, before any analysis, let the agent **ask you**, and
write the answers down.

This is the last exercise. It is about **your own** research, and you take
home a ready start for using an agent in it.

- You need: [Exercise 1](02-pendulum.md). The ideas of Exercises 4, 8, 9 and
  11 help you go on afterwards.
- Time: about 40 minutes.
- You learn: interview mode, answers kept in your own words and labelled as
  recollection, open questions as a table, and draft rules for your project.

<details>
<summary>Explain: the research project of the demos does this too</summary>

In the research repository shown in [Exercise 9](11-registry.md), the agents
keep a table of open questions: the question, why it matters, who can answer
it, and which facts the answer would change. A script prints one sheet per
person, in the order of a session. Then the agent and the person sit down for
a **Q&A session**: the agent asks, the person answers, and the agent writes a
note with the answers **verbatim**, labelled *recollection*. Only then are the
facts in the registry updated, each citing the note.

Two lessons from it:

- **Ask the person, not the model.** A model asked about your experiment
  invents a plausible answer. A person answers what happened, or says "I do
  not know", which is also worth writing down.
- **Keep the words.** A summary written later loses the "I think" and the
  "probably". The verbatim answer keeps how sure the person was.

</details>

## Part A: prepare

### A1. Create the project folder

Create the folder `kisd-myproject` and open it in opencode, as in
[Create a project folder](01-project-folder.md). Do **not** put your data in
it yet.

### A2. Get the interview rules

```text
Download this file into the project folder as AGENTS.md:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/interview/AGENTS.md
Then show me its content. Do nothing else.
```

Expected result: the rules of **interview mode**. In short: one question at a
time, your answers written down in your own words, open questions in
`questions.csv`, no code and no access to files outside the folder. At the
end, a description of your data and draft rules for your project.

Start a new session (**+** or **Ctrl+T**), so that the agent reads the rules.

## Part B: the interview

### B1. Start

```text
Interview me about my research project and my data. Start.
```

Expected result: one sentence about what is sent to the model, then the first
question, often with choices to click (the question tool of
[Exercise 2](04-meteo.md)). Answer as you would answer a new colleague. "I do
not know" and "I would have to check" are good answers: they become open
questions.

<!-- TODO: screenshot of the first interview question. -->

**Privacy:** everything you type goes to the model at llmlab, as in
[Exercise 4](06-outside-folder.md). Describe your data; do not paste personal
data of study participants, passwords, or anything you may not share.

### B2. Keep going

Answer for 15–20 minutes. After every five questions the agent sums up what it
knows. Correct it there: a wrong summary now becomes a wrong rule later.

Open `notes/` in the right panel (**Open file**) now and then. Are your answers
there, in your words?

If the questions stay too general, steer:

```text
Ask me now about the data itself: what one file or one row is,
the units, and the known problems.
```

## Part C: what you take home

### C1. Finish

```text
Finish.
```

Expected result: four files.

| File | What it is |
|---|---|
| `notes/interview-<date>.md` | Every question and your answer, labelled *recollection* |
| `questions.csv` | What is still open, and who could answer it |
| `README-data.md` | A description of your data, from your answers only, each statement citing the note |
| `AGENTS.draft.md` | Draft rules for an agent in your project |

The agent also names the three most important open questions. Who can answer
them: you, your supervisor, or the data?

### C2. A second opinion on the summary

As in [Exercise 10](12-second-opinion.md), let a fresh agent check that the
summary says only what you said. Start a **new session**:

```text
Check README-data.md against notes/. List every statement in
README-data.md that the notes do not support. Do not change anything.
```

Expected result: a short list, or none. Each item is something the agent
added from its own idea of your field. Correct it, or answer it as a new
question.

### C3. Read the draft rules

Open `AGENTS.draft.md`. Does it say where your data is, that it is read-only,
what to cite, and what never to do? Change what is wrong, by hand or with the
agent. This is the start of the `AGENTS.md` of your real project.

## After the course: your own project

The course in one list. In the folder of your real project:

1. **Rules:** rename `AGENTS.draft.md` to `AGENTS.md`
   ([Learning with an agent](03-learning-with-an-agent.md)). Keep the
   learning mode of [Exercise 2](04-meteo.md) as long as you want to learn.
2. **Data:** keep the raw data read-only, in a folder outside the project, and
   allow only that folder ([Exercise 4](06-outside-folder.md)). Put a
   `.venv` in the project ([Exercise 1](02-pendulum.md)).
3. **Literature:** a `raw/` folder and a wiki that the agent keeps
   ([Exercise 7](09-literature.md), [Exercise 8](10-wiki.md)).
4. **Facts:** the numbers your thesis depends on, in a table with *basis* and
   *checked* ([Exercise 9](11-registry.md)); open questions in
   `questions.csv`.
5. **Checks:** turn the rules that matter into scripts that fail
   ([Exercise 11](13-guardrails.md)), and let a second agent or model check
   what matters most ([Exercise 10](12-second-opinion.md)).
6. **You:** read what the agent wrote, predict before it runs, and check the
   results as you would check a new colleague's work.

## If something goes wrong

- **The agent starts analysing, or asks for your files.** Remind it: "This is
  an interview. Ask me; do not open files." The rules in `AGENTS.md` say so.
- **The agent asks several questions at once.** Answer the first, and say:
  "One question at a time, as AGENTS.md says."
- **The notes summarise instead of quoting.** Ask: "Write my answers in my
  own words, as AGENTS.md says." Compare one answer with what you typed.
- **You have no project of your own yet.** Interview mode works on any
  project: let the agent interview your neighbour about theirs, or answer
  about the weather data of [Exercise 2](04-meteo.md) as if it were yours.

<details>
<summary>Going further</summary>

- **Interview your supervisor.** Send them `questions.csv`, or sit together
  and let the agent ask the questions marked *supervisor*. Add the answers as
  a new note, with their name.
- **A second round.** After a week of work, start a new interview session:
  "Read the notes and questions.csv, and ask me only the open questions."
- **Facts from the interview.** Turn the answers that your analysis depends
  on into rows of `facts.csv`, with basis *recollection* and checked
  *untested*, as in [Exercise 9](11-registry.md). Then check them against the
  data.

</details>

---

← Previous: [Exercise 11](13-guardrails.md) · [Exercises overview](README.md) · [Main README](../README.md)
