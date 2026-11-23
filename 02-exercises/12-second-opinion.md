# Exercise 10: a second opinion

In [Exercise 5](07-parallel.md), nobody compared the results of the
subagents, and a broken wind vane went unnoticed. In [Exercise 8](10-wiki.md),
the agent wrote a wiki with page numbers. Are they right? Checking every page
yourself takes long. Let another agent do the first pass: a **checker** that
may only read, and a **second model** that answers the same questions
independently.

- You need: [Exercise 8](10-wiki.md) done, in the folder `kisd-wiki`.
- Time: about 30 minutes.
- You learn: a read-only checker subagent, checking citations page by page,
  why a check by the same model is weaker, and comparing two models.

<details>
<summary>Explain: why a second agent helps, and where it does not</summary>

- A **fresh** agent has not written the page, so it has no reason to defend
  it. It reads the source page and compares.
- A **read-only** agent cannot "fix" the page to make it fit. It can only
  report. You decide.
- But an agent of the **same model** has the same habits: the same
  misreadings of an old scan, the same knowledge from training. Its errors and
  the writer's errors are not independent. A **second model** makes other
  mistakes, so where the two agree, you can trust more; where they differ,
  you look.

</details>

## Part A: the checker

### A1. Get the checker

Long addresses stay on their own lines:

```text
Download this file as .opencode/agents/checker.md:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/review/checker.md
Then show me its content. Do nothing else.
```

Expected result: a file that defines a subagent **checker**. Its permissions
forbid any edit, as in [Exercise 3](05-limits.md). Its task: for every cited
statement, open that page of the source, and say *confirmed*, *not found* or
*contradicted*, with a short quote as evidence.

Close the opencode app completely and open it again, so that it loads the new
subagent. Start a new session.

### A2. Check two pages

```text
Use the checker subagent to check wiki/concepts/regression.md
and wiki/people/francis-galton.md. Show me its table.
```

Expected result: one subagent call, then a table. Most statements are
*confirmed*. Look at the others:

- **Not found** often means a wrong page number. The scan of Galton's paper
  has its own PDF pages; the journal pages printed on them are different
  (PDF page 1 is journal page 246, and the plates have no journal page). A
  wiki that mixes the two cites the wrong page.
- **Contradicted** is rare, and serious: read that source page yourself.
- Statements **without a source** are listed at the end.

<!-- TODO: screenshot of the checker's table. -->

### A3. Fix, then check again

```text
Fix the statements that the checker marked "not found" or
"contradicted", using the sources. Then run the checker again
on the same two pages.
```

Expected result: edits by the main agent (not by the checker), then a second
table with fewer problems.

**What to notice:** the writer and the checker have different jobs, and
different permissions. The checker cannot change what it judges.

## Part B: a second model

The same questions, answered by two models, each in its own session, with no
wiki: only the sources. In opencode, the model is chosen under the text box
(or with `/model`), see
[First steps](../01-getting-started/05-first-steps.md).

### B1. Model one

Start a new session with **DeepSeek V4.1 Flash (PLGrid)**:

```text
Answer from the files in raw/ only, not from the wiki and not from
memory. Give the file and page (journal page where printed) for each.
1. On which page does Galton state the ratio of the children's
   deviation to the mid-parents', and what is it?
2. How many adult children and how many mid-parents does Table I count?
3. By which factor were female heights multiplied?
4. How many sons and daughters remain in the data of Han et al.?
Save the answers as results/answers-deepseek.md.
```

### B2. Model two

Start a new session, choose **Gemma 4 31B (PLGrid)**, and paste the same
prompt. Only the file name in the last line differs:

```text
Answer from the files in raw/ only, not from the wiki and not from
memory. Give the file and page (journal page where printed) for each.
1. On which page does Galton state the ratio of the children's
   deviation to the mid-parents', and what is it?
2. How many adult children and how many mid-parents does Table I count?
3. By which factor were female heights multiplied?
4. How many sons and daughters remain in the data of Han et al.?
Save the answers as results/answers-gemma.md.
```

If your grant can use only one of the two models, pair up with a neighbour
who has the other.

### B3. Compare

```text
Compare results/answers-deepseek.md and results/answers-gemma.md.
Make a table: question, answer of each model, same or different.
Do not decide who is right.
```

Expected result: a table. The right answers are: (1) journal page 252,
two-thirds; (2) 928 children and 205 mid-parents, page 248; (3) 1.08, page
247; (4) 481 sons and 453 daughters, pages 2–3 of the arXiv paper. Where the
two models differ, look at the source page yourself. A common difference: one
model gives the PDF page, the other the journal page.

## Take home

1. **Let a fresh agent check**, not the one that wrote it.
2. **Make the checker read-only.** It reports; you decide; the writer fixes.
3. **Check citations page by page**: a quote from the source is the evidence.
4. **A second model is a more independent check** than a second run of the
   same one.
5. **Checks cost tokens.** Check what your result depends on, not every line.

## If something goes wrong

- **opencode says it does not know the checker.** Check that the file is at
  `.opencode/agents/checker.md` in the project folder (note the dot), and
  restart opencode.
- **The checker edits a page.** It should not be able to. Check the
  permissions block in `checker.md`. A Shell command can still write a file,
  as in [Exercise 3, part D](05-limits.md#part-d-the-gap-in-the-lock): look
  at its tool calls.
- **The checker confirms everything, fast.** Ask the main agent: "Did the
  checker extract the text of each cited page? Show me its tool calls." A
  checker that did not open the pages has checked nothing.

<details>
<summary>Going further</summary>

- **Check the facts.** Ask the checker to check every row of `facts.csv`
  from [Exercise 9](11-registry.md) against its source.
- **A checker on the other model.** Add the line `model:
  plgrid/google/gemma-4-31B` to the header of `checker.md`, so that the
  checker always runs on a different model from the writer.
- **Two transcriptions.** Let both models transcribe Table I of Galton's
  paper, as in [Exercise 7](09-literature.md), and compare cell by cell.

</details>

---

← Previous: [Exercise 9](11-registry.md) · [Exercises overview](README.md) · Next: [Exercise 11 →](13-guardrails.md)
