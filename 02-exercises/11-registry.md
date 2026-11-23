# Exercise 9: facts with a source

The wiki of [Exercise 8](10-wiki.md) is easy to read, but it does not say how
sure each statement is. A logbook entry, a number computed from the data and a
colleague's memory all look the same on a page. In a research project, you
need to tell them apart: which facts were **checked**, and which were only
**written down** somewhere?

This page has two parts: a **demo**, in which the lecturer shows how a real
research project keeps its facts (with Claude Code), and an **exercise**, in
which you do the same, small, for the Galton facts in your `kisd-wiki`.

- You need: [Exercise 8](10-wiki.md) done, in the folder `kisd-wiki`.
- Time: 15 minutes demo, 25 minutes exercise.
- You learn: the order of sources, the labels *basis* and *checked*, why a
  document is not a check, what to do when sources disagree, and generated
  files that nobody edits by hand.

## Part A: demo, a research project's facts

The lecturer opens a real research repository in Claude Code: a measurement
campaign with proton beams, where the analysis is being redone from the raw
data, with agents. The repository is private; you only watch. What to look at:

1. **Where the truth lives, in this order**, written at the top of its
   `AGENTS.md`:
   1. raw data (read-only, with a checksum of every file);
   2. `evidence/`: logbook transcriptions, notes of what people said, data
      checks. Files are **added, never edited**;
   3. `registry/`: the **only** place where facts are written by hand, as rows
      of CSV tables, each citing its evidence;
   4. everything **generated** from the registry: pages, run tables. Never
      edited by hand; rebuilt by scripts.
2. **Two labels on every value.** *basis*: how it is known (document,
   measurement, inference, recollection, prediction). *checked*: whether a
   check or a second source agrees (agrees, disagrees, untested,
   untestable). The rule that matters most: **a logbook line is a document,
   not a check**. It stays *untested* until something compares it with the
   data.
3. **Old analyses are hypotheses.** Results from before the rules were set
   are kept, but labelled "legacy, unverified", and never copied as facts.
4. **Following one claim** from its row in the registry to the data check
   that supports it, and on to the raw files.
5. **Open questions** are rows of a table too: the question, why it matters,
   who can answer it, and which facts the answer would change.
   [Exercise 12](14-interview.md) uses this idea.

Ask Claude live, for example: "Which facts about this day rest only on what
someone remembers?" It answers from the labels in seconds, because the labels
are data, not prose.

## Part B: exercise, the Galton facts

Open the project `kisd-wiki` in opencode and start a new session.

### B1. Add the rules

The schema grows with the project. Add the rules for facts to `AGENTS.md`:

```text
Add a section "Facts" to AGENTS.md, with these rules:
- Facts that matter are rows of facts.csv, with the columns
  id, fact, value, basis, checked, source, note.
- basis says how the value is known: document (a primary source states
  it), measurement (a script in scripts/ computed it from data),
  inference (a secondary source says it, or the sources do not settle it),
  recollection (what a person said later).
- checked says whether a check or a second, independent source agrees:
  agrees, disagrees, untested, untestable.
- A document is not a check. A value from one document is untested.
- When sources disagree, write both into the value, with checked
  disagrees, and say in the note which source is stronger and why.
- wiki/facts.md is generated from facts.csv by scripts/build_facts.py.
  Never edit it by hand.
Show me the new section.
```

Start a **new session**, so that the agent reads the changed `AGENTS.md`.

### B2. Write the facts

```text
Create facts.csv with these facts, from the sources in raw/ only.
F1: the year Galton first spoke in public about "regression".
F2: the word Galton used for the effect in 1877.
F3: the ratio of the children's deviation to the mid-parents'.
F4: the number of adult children in Galton's data.
F5: the factor for female heights.
F6: the date of Galton's 1877 lecture at the Royal Institution.
Give each fact its basis, checked and source with page or section.
Show me the table.
```

Expected result: a table close to this. Compare, and ask about any row that
differs:

| Fact | Value | basis | checked |
|---|---|---|---|
| F1 | 1885, Aberdeen (Galton 1886, p. 246) | document | untested |
| F2 | reversion (Wikipedia, "Francis Galton") | **inference**: the 1877 lecture itself is not in `raw/` | untested |
| F3 | 2 to 3 (Galton 1886, p. 252, and Plate IX) | document | untested |
| F4 | 928 (Table I, p. 248), 930 (text, p. 247), 934 (Han et al., from the notebook) | document | **disagrees** |
| F5 | 1.08 (Galton 1886, p. 247 and Table I) | document | untested |
| F6 | 9 February 1877 (Galton 1886, p. 246); Wikipedia says 9 and also 19 | document | a good note says that Galton's own text is the stronger source |

**What to notice:** F2 is true, but in this project it is only an
*inference*: no primary source in `raw/` shows it. To make it a *document*,
you would add Galton's 1877 lecture to `raw/` (Going further).

### B3. A check with data

Galton's ratio 2/3 is a document. A computation can test it. The nine medians
of Table I are given here, so you do not need to read the table:

```text
Check F3 with data. Write scripts/check_ratio.py: fit a straight line
to these 9 points (mid-parent height, median height of the children):
64.5 65.8, 65.5 66.7, 66.5 67.2, 67.5 67.6, 68.5 68.2,
69.5 68.9, 70.5 69.5, 71.5 69.9, 72.5 72.2
(Galton 1886, Table I, p. 248). Run it, and update F3 in facts.csv.
```

Expected result: a slope of about **0.685**, and F3 updated: *checked*
`agrees` (0.685 is close to 0.667), with the script named in the source.
Is "close" close enough? The rule says the note must say why: ask for it if
it is missing.

### B4. A generated page

```text
Write scripts/build_facts.py. It reads facts.csv and writes
wiki/facts.md: a table, with this line on top:
"Generated from facts.csv by scripts/build_facts.py. Do not edit."
Run it, and add wiki/facts.md to wiki/index.md.
```

Expected result: a script, a run, and a new page. Open `wiki/facts.md`.

### B5. Test the rule

```text
In wiki/facts.md, change the ratio in F3 to 0.685.
```

Expected result: a good agent does **not** edit `wiki/facts.md`. It changes
`facts.csv` (or asks you first, since 0.685 is the check, not Galton's
statement), and runs `scripts/build_facts.py` again.

If it edits `wiki/facts.md` directly, the rule in `AGENTS.md` was only a
request, as you know from [Exercise 3](05-limits.md). In
[Exercise 11](13-guardrails.md) you turn such a rule into a check that runs.

## Take home: wiki or registry?

| | Wiki ([Exercise 8](10-wiki.md)) | Registry (this page) |
|---|---|---|
| Good for | Reading, finding, connecting | Facts that a result depends on |
| Form | Pages of prose, with links | Rows, with labels |
| How sure? | You read it in the text, if at all | *basis* and *checked* on every value |
| Changes | The agent rewrites pages | One edited place; the rest is generated |

1. **Order your sources**: raw data, then documents and notes, then your own
   tables, then everything generated.
2. **Label how each value is known**, and whether anything checked it.
3. **A document is not a check.** Count what is only written down.
4. **When sources disagree, keep both** and say which is stronger, and why.
5. **Never edit a generated file.** Change the source, and rebuild.

## If something goes wrong

- **The agent fills `checked` with `agrees` for everything.** Ask: "Which
  check or second, independent source agrees with F1?" Two pages of the same
  paper are not independent.
- **The agent uses its memory for F2.** Ask it which file in `raw/` says so.
  If none, the value is not a fact of this project yet.
- **`facts.csv` opens with all columns in one** (in Excel). Values contain
  commas. Open it in the right panel of opencode instead, or ask the agent to
  quote every value.

<details>
<summary>Going further</summary>

- **Make F2 a document.** Ingest Galton's 1877 lecture into the wiki
  ([galton.org](https://galton.org/essays/1870-1879/galton-1877-roy-soc-typical-laws-heredity.pdf);
  the word is defined on journal page 291) and update F2.
- **Open questions as rows.** Ask the agent to write `questions.csv` for what
  the sources do not settle (which 928 of the 934 children did Galton use?),
  with the columns question, why, who could answer.
- **Your own project.** Which ten numbers does your thesis depend on? Write
  them as `facts.csv`, with honest labels. How many are *untested*?

</details>

---

← Previous: [Exercise 8](10-wiki.md) · [Exercises overview](README.md) · Next: [Exercise 10 →](12-second-opinion.md)
