# kisd-2026-agents-coding

Lecture materials on **agentic coding for data analysis**, using
[opencode](https://opencode.ai) and [llmlab at Cyfronet](https://llmlab.plgrid.pl)
(PLGrid Forge). The course is for PhD students of the KISD doctoral school.

**When and where:** 3 November 2026 (03.11.2026), 11:30–14:00, MSD room,
IFJ PAN (Institute of Nuclear Physics, Polish Academy of Sciences, Kraków).

**Where to start:** the folders are numbered in the order you use them. Read
the five pages in `01-getting-started` (they set up opencode on your computer),
then do the tasks in `02-exercises`, in order.

## Contents

1. [`01-getting-started/`](01-getting-started/README.md) — what an agent is, and setting up opencode with llmlab:
   1. [Chatbot or agent](01-getting-started/01-chatbot-or-agent.md)
   2. [Install the opencode app](01-getting-started/02-install-opencode.md)
   3. [Configure opencode for llmlab, part 1: the address](01-getting-started/03-configure-llmlab-endpoint.md)
   4. [Configure opencode for llmlab, part 2: the API key](01-getting-started/04-configure-llmlab-api-key.md)
   5. [First steps in opencode](01-getting-started/05-first-steps.md)
2. [`02-exercises/`](02-exercises/README.md) — hands-on tasks with copy-paste prompts:
   0. [Before the exercises: check that Python is installed](02-exercises/00-prerequisites.md)
   1. [Before each exercise: create a project folder](02-exercises/01-project-folder.md)
   2. [Exercise 1: your first agent session — measuring *g* with a pendulum](02-exercises/02-pendulum.md)
   3. [Learning with an agent: sessions, AGENTS.md and learning mode](02-exercises/03-learning-with-an-agent.md)
   4. [Exercise 2: twenty years of weather in Katowice](02-exercises/04-meteo.md)
3. Later, optional: [opencode and Claude Code](01-getting-started/06-opencode-vs-claude-code.md),
   [For experts: other ways to set up](01-getting-started/07-for-experts.md)

The settings file that page 3 downloads is in
[`01-getting-started/config/`](01-getting-started/config/README.md).

## How this repository was made

All the content of this repository (pages, prompts and example files) was
created by the AI model **Claude Opus 5.5** (Anthropic). It took many iterations
and strict guidelines from the author, Leszek Grzanka. You can read those
guidelines in [CLAUDE.md](CLAUDE.md). The screenshots come from the author's
own runs of the exercises.

## For contributors

- [Guidelines for creating content](CLAUDE.md) — audience, copy-paste rules, folder READMEs
- [License](LICENSE)
