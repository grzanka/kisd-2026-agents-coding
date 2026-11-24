# Exercise 11: rules that run

Since [Exercise 2](04-meteo.md) you have written rules into `AGENTS.md`. In
[Exercise 3](05-limits.md) you saw their limit: a rule there is a **request**
to the model. It usually follows it, but nothing makes it. Permissions in
`opencode.json` are enforced by the app, but they can only allow or forbid a
tool. Most rules of a project are about **content**: "every page cites a
source", "never edit a generated file". Those can be turned into a **check**:
a small program that runs, and fails when a rule is broken.

This page has a **demo** (the lecturer, with Claude Code, on a real research
project) and an **exercise** (you, with opencode, in `kisd-wiki`).

- You need: [Exercise 8](10-wiki.md) and [Exercise 9](11-registry.md) done,
  in the folder `kisd-wiki`.
- Time: 15 minutes demo, 25 minutes exercise.
- You learn: three levels of rules (request, permission, check), turning a
  rule into a script, and what makes a check run without the model.

<details>
<summary>Explain: request, permission, check</summary>

| Level | Where | Who enforces it | Example |
|---|---|---|---|
| Request | `AGENTS.md` | The model, if it follows it | "Cite a source on every page" |
| Permission | `opencode.json` | The app, before each tool call | "Never edit files in `raw/`" |
| Check | A script or a test | Whoever runs it: the agent, you, or a program that runs it automatically | `check_wiki.py` fails if a page has no sources |

A check is only as strong as the thing that runs it. If the agent is asked to
run it, it is still a request, but one with a clear, visible result. If
something outside the model runs it (a git hook, an automatic test on GitHub,
a hook of the agent app), the model cannot skip it.

</details>

## Part A: demo, checks in a research project

The lecturer opens the research repository of
[Exercise 9](11-registry.md) in Claude Code. What to look at:

1. **One rules file for every agent.** `AGENTS.md` holds the rules;
   `CLAUDE.md` imports it with one line (`@AGENTS.md`) and adds only what is
   special to Claude Code. GitHub Copilot reads the same rules. One truth, no
   copies that drift apart.
2. **Tests that guard the rules themselves.** A test reads `AGENTS.md`,
   `CLAUDE.md` and the skill files, and fails if any of them contains a
   physics number with a unit, or a path into an old analysis. Rules files
   hold rules, never results.
3. **Tests that guard generated files.** A test rebuilds the generated pages
   from the registry and fails if they differ from what is in the repository.
   A hand edit of a generated page cannot survive: the rule of
   [Exercise 9, B5](11-registry.md#b5-test-the-rule), enforced.
4. **The registry refuses nonsense.** A test fails on an unknown field, or on
   a number in a typed column that does not appear in the text of the value.
5. **Skills: procedures loaded when needed.** A skill is a Markdown file of
   instructions that Claude Code loads only when the task needs it, for
   example how to work on the shared computing cluster ("the login node runs
   seconds, not data"), or how to open a pull request in this repository. The
   rules of every session stay short.
6. **Worktrees for parallel agents.** Each agent works in its own copy of the
   repository, on its own branch (a git *worktree*). The rule of
   [Exercise 5](07-parallel.md), "one task, one output folder", made
   structural.
7. **Before every pull request, the full test suite.** `CLAUDE.md` says so,
   and the agent runs it, about 70 seconds on 8 processes. Watch what happens
   when a test fails: the agent reads the failure and fixes the cause.

## Part B: exercise, a check for your wiki

Open the project `kisd-wiki` in opencode and start a new session.

### B1. Write the check

```text
Write scripts/check_wiki.py. It checks the wiki and prints every problem:
- a page in wiki/ (except index.md and log.md) without a "Sources:" section;
- a relative link in wiki/ that points to a file that does not exist;
- a page in wiki/ that is not listed in wiki/index.md;
- wiki/facts.md differs from what scripts/build_facts.py makes now.
It ends with exit code 1 if there is a problem, else 0 and "wiki OK".
Run it, and show me its output. Do not fix anything yet.
```

Expected result: a script, a run, and a list of problems. Most wikis have a
few after Exercises 8 to 10: a page missing from the index, a link to a page
that was never written. If it prints "wiki OK" at once, break a rule on
purpose (B4) to see that the check works.

### B2. Fix until it passes

```text
Fix the problems that check_wiki.py found, then run it again.
Repeat until it prints "wiki OK".
```

Expected result: some edits, and runs of the script, until "wiki OK".

**What to notice:** "fix until the check passes" is a loop that agents do well.
The check tells the agent exactly what is wrong, and when it is done.

### B3. Make it part of the work

```text
Add to the section "Operations" of AGENTS.md: "After every ingest and
every edit of wiki/, run scripts/check_wiki.py. You are done only when it
prints wiki OK." Show me the change.
```

Start a **new session**, so that the agent reads the changed `AGENTS.md`.
Then give it a new source:

```text
Download this file into raw/ as galton-1877.pdf:
https://galton.org/essays/1870-1879/galton-1877-roy-soc-typical-laws-heredity.pdf
Then ingest raw/galton-1877.pdf.
```

Expected result: the ingest, as in Exercise 8, and at the end a run of
`check_wiki.py`, perhaps a fix, and "wiki OK". The new source defines the word
**reversion** (journal page 291): a good moment to update F2 of
[Exercise 9](11-registry.md) from *inference* to *document*.

If the agent finishes without running the check, the rule was a request, and
the model skipped it. Say: "Run the check, as AGENTS.md says."

### B4. Does the check catch it?

Break a rule yourself, by hand: open `wiki/facts.md` in any text editor (for
example Notepad), change one number, and save. Then:

```text
Run scripts/check_wiki.py.
```

Expected result: the check reports that `wiki/facts.md` differs from what
`build_facts.py` makes. Ask the agent to rebuild it.

**What to notice:** the check does not care who broke the rule: an agent, a
colleague, or you.

## Take home

1. **Three levels:** a request (`AGENTS.md`), a permission (`opencode.json`),
   a check (a script that fails).
2. **Turn the rules that matter into checks.** "Every page cites a source"
   becomes ten lines of Python.
3. **Make the check print what is wrong.** Then "fix until it passes" is an
   easy loop for the agent.
4. **A check that the agent is asked to run is still a request.** Let
   something outside the model run it, for what really matters.
5. **Keep rules files short.** Procedures for special tasks go into separate
   files (skills), loaded when needed.

## If something goes wrong

- **The check reports hundreds of problems.** It may treat web links
  (`https://...`) as file links. Ask: "Check only relative links."
- **The agent changes the check so that it passes.** Look at the edits: a
  check that was weakened is not a fix. Say: "Do not change
  check_wiki.py; fix the wiki."
- **`build_facts.py` does not exist.** You skipped Exercise 9. Ask the agent
  to leave out the last item of the check.

<details>
<summary>Going further</summary>

- **A git hook.** If your project is a git repository, ask the agent to add
  the check as a *pre-commit hook*: git then runs it before every commit,
  whoever commits.
- **A hook of the agent app.** Claude Code can run a command when the agent
  wants to stop, and send its output back if it fails, so the agent cannot
  finish while the check fails. See "Hooks" in the Claude Code documentation;
  opencode has plugins for similar purposes.
- **Your own project.** Which rule of your analysis have you broken by
  accident before (a unit, a file that must not change, a column that must
  not be empty)? Ask the agent to write a check for it.

</details>

---

← Previous: [Exercise 10](12-second-opinion.md) · [Exercises overview](README.md) · Next: [Exercise 12 →](14-interview.md)
