# Rules for this project: a wiki that you keep

We build a wiki about one research topic: where the statistical word
"regression" comes from. I collect the sources; you write and keep the wiki.
The idea follows Andrej Karpathy's "LLM Wiki" (2026).

- Before each tool call, say in one sentence what you will do and why.
- Run Python only with .venv\Scripts\python.exe (on Linux and macOS:
  .venv/bin/python). Install packages only into .venv.
- Do not read a whole PDF into the conversation. Extract the pages you need.
- Never put your own memory into the wiki. Every statement comes from a
  source in raw/, with the page or section.

## Layout

- raw/ holds the sources. Never change, rename or delete them.
- wiki/ is yours. You write every page; I read them.
  - wiki/index.md: every page, with a link and one line, grouped under
    Sources, People, Concepts, Answers.
  - wiki/log.md: append only. Each entry starts with
    `## [YYYY-MM-DD] ingest | title`, `query` or `lint`.
  - wiki/sources/: one page per file in raw/: what it is, the key points
    with page numbers.
  - wiki/people/, wiki/concepts/: one page per person or idea.
  - wiki/answers/: answers I asked you to keep.
- Every page: a title, a summary of 2-3 sentences, the text, links to
  related pages (relative Markdown links), and at the end a section
  "Sources:" with the raw/ file and page of every statement.

## Operations

- **Ingest FILE:** read the source, tell me its 3-5 key points, then write
  its source page, update or create the people and concept pages it
  touches, update index.md, and add a log entry. If the new source says
  something different from a page, do not overwrite: add a section
  "Disagreements" to that page, with both statements and their sources.
- **Query:** read index.md first, then the pages you need. Answer with
  links to the wiki pages and the sources with pages. If I say "file it",
  save the answer in wiki/answers/, add it to index.md and to the log.
- **Lint:** look for disagreements, statements without a source, pages
  that no other page links to, pages missing from index.md, and ideas that
  are mentioned but have no page. Report a list. Fix only what I approve.
