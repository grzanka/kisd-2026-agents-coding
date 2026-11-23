# Exercise 8: a wiki that the agent keeps

A session forgets everything when it ends
([Learning with an agent](03-learning-with-an-agent.md)). Your files stay. So
let the agent write what it learns into files, and keep them up to date: a
small **wiki**, a set of linked Markdown pages about your topic. Each new
paper you read makes it richer, and each new session starts from it instead of
from zero.

The pattern comes from Andrej Karpathy, who published it in April 2026 as an
"idea file" to give to an agent:
[LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).
You use it on the topic of [Exercise 7](09-literature.md): where the word
*regression* comes from.

- You need: everything from [Exercise 1](02-pendulum.md). Exercise 7 helps,
  but is not required.
- Time: about 40 minutes. Exercises 9 to 11 continue in the same folder.
- You learn: raw sources versus a compiled wiki, the operations ingest, query
  and lint, and how a new session uses what an old one wrote.

<details>
<summary>Explain: three layers, three operations</summary>

| Layer | Who writes it | Here |
|---|---|---|
| **Raw sources**: papers, articles, data | You collect them; nobody changes them | `raw/` |
| **The wiki**: summaries, pages per person and idea, an index, a log | The agent, only | `wiki/` |
| **The schema**: how the wiki is built and kept | You and the agent, over time | `AGENTS.md` |

- **Ingest:** a new source goes into `raw/`; the agent reads it and updates
  every page it touches.
- **Query:** you ask; the agent reads `wiki/index.md` first, then the pages,
  and answers with links. A good answer can become a page.
- **Lint:** the agent checks the wiki for disagreements, statements without a
  source, and pages that nothing links to.

The difference from uploading files to a chatbot: there, the model searches
the files again for every question, and nothing builds up. Here, the work of
reading is done once and kept.

</details>

## Part A: prepare

### A1. Create the project folder

Create the folder `kisd-wiki` and open it in opencode, as in
[Create a project folder](01-project-folder.md).

### A2. Get the schema and the sources

Long addresses stay on their own lines:

```text
Download these files into the project folder. The first one as AGENTS.md:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/main/02-exercises/wiki/AGENTS.md
The next ones into raw/, with the names given:
https://galton.org/essays/1880-1889/galton-1886-jaigi-regression-stature.pdf
as raw/galton-1886.pdf,
https://arxiv.org/pdf/1508.02942
as raw/han-2015-arxiv.pdf,
https://en.wikipedia.org/w/index.php?title=Regression_toward_the_mean&action=raw
as raw/wikipedia-regression-toward-the-mean.txt,
https://en.wikipedia.org/w/index.php?title=Francis_Galton&action=raw
as raw/wikipedia-francis-galton.txt.
Then list raw/ with the file sizes. Do nothing else.
```

Expected result: five files. In `raw/`: two PDFs of about 1 MB each, and two
text files of about 40 and 80 kB.

### A3. Restart, and prepare the tools

Start a new session (**+** or **Ctrl+T**), so that the agent reads
`AGENTS.md`. Then:

```text
Create .venv and install pymupdf into it. Do nothing else.
```

## Part B: ingest

### B1. The first source

```text
Ingest raw/galton-1886.pdf.
```

Expected result: the agent extracts text from some pages, tells you 3-5 key
points (the "law of regression", heights of 205 mid-parents and their adult
children, the ratio 2 to 3), then writes several files: a page in
`wiki/sources/`, pages such as `wiki/people/francis-galton.md` and
`wiki/concepts/regression.md`, `wiki/index.md` and `wiki/log.md`.

Open `wiki/index.md` in the right panel (**Open file**), and follow a link or
two. Does every page end with "Sources:", with page numbers?

### B2. A second source

```text
Ingest raw/han-2015-arxiv.pdf.
```

Expected result: one new source page, and **edits** of the existing pages.
Look for what changed in `wiki/concepts/regression.md`: the new source adds
the family data, Galton's notebook, and a second view of the 2/3.

**What to notice:** the second source did not make a second summary. It was
woven into the pages that already existed. That is the point of a wiki.

### B3. The two Wikipedia articles

```text
Ingest raw/wikipedia-regression-toward-the-mean.txt,
then raw/wikipedia-francis-galton.txt.
```

Expected result: two more source pages, more edits, and probably a section
**"Disagreements"** on some pages. The article on regression toward the mean
says that Galton "popularized" the concept, and two paragraphs later that he
"coined" the term. The article on Galton gives the date of his 1877 lecture
once as 9 February and once as 19 February.

If no "Disagreements" section appears, do not worry: part C finds them.

## Part C: use it

### C1. A new session knows it all

Start a **new session**. It has never seen the sources. Then:

```text
When and where did Galton first speak about "regression",
and what had he called the effect before?
```

Expected result: a **Read** of `wiki/index.md`, then of two or three pages,
and an answer with links: the Aberdeen address of 1885, the 1886 paper, the
sweet pea experiments presented on 9 February 1877. Few tool calls, no PDF
read again.

If the answer is good:

```text
File it.
```

Expected result: a new page in `wiki/answers/`, a line in `wiki/index.md`, an
entry in `wiki/log.md`.

### C2. Lint

```text
Lint the wiki.
```

Expected result: a list, for example: the 9 versus 19 February; "coined" versus
"popularized"; a statement without a page number; an idea that has no page
yet (Karl Pearson, least squares). Pick two items and let the agent fix them:

```text
Fix the first two items. Leave the rest.
```

**What to notice:** the agent finds where the sources disagree, but it cannot
settle it. For that you need the primary source, or a rule for which source
wins: that is [Exercise 9](11-registry.md).

## Take home

1. **Keep knowledge in files, not in sessions.** A new session reads the
   index and is up to date in seconds.
2. **Raw sources are never changed.** The wiki can always be rebuilt from
   them.
3. **One source touches many pages.** Ingest one source at a time, and read
   what changed.
4. **Lint regularly.** A wiki that nobody checks collects contradictions.
5. **The schema grows with you.** When something bothers you, change
   `AGENTS.md`, not only the page.

## If something goes wrong

- **Wikipedia answers with error 403.** Tell the agent: "Use the webfetch
  tool, or set a User-Agent header that names this project."
- **The agent writes one long summary instead of pages.** Ask: "Follow the
  layout in AGENTS.md: one page per source, person and idea, and index.md."
- **The agent writes things from memory into the wiki.** Ask it to show the
  source and page for that statement. If there is none, ask it to remove it.
- **The wiki becomes hard to read in the right panel.** Open the folder
  `wiki` in any Markdown viewer. Karpathy uses
  [Obsidian](https://obsidian.md): its graph view shows which pages are
  linked.

<details>
<summary>Going further</summary>

- **Let the agent write the schema.** In a new, empty folder, give the agent
  Karpathy's idea file (the link above) and your own topic, and let it
  propose its own `AGENTS.md`. Compare with ours.
- **Your own topic.** Put 3-5 papers of your field into `raw/`, one at a
  time, and ingest them. After a week, ask a question that needs all of them.
- **Search.** Past about a hundred pages, the index is not enough. Ask the
  agent to write a small search script over `wiki/`.

</details>

---

← Previous: [Exercise 7](09-literature.md) · [Exercises overview](README.md) · Next: [Exercise 9 →](11-registry.md)
