# 2. Exercises

Hands-on tasks for the lectures. In each one you give an AI agent (opencode)
short prompts and watch it read, write and run files on your computer. All
prompts are ready to copy and paste. You do not need to know Python.

| # | Exercise | What you learn |
|---|---|---|
| 0 | [Before the exercises: check that Python is installed](00-prerequisites.md) | Check Python and pip, install Python if it is missing |
| 1 | [Before each exercise: create a project folder](01-project-folder.md) | One project = one folder; create it from inside opencode |
| 2 | [Exercise 1: your first agent session — measuring *g* with a pendulum](02-pendulum.md) | How an agent differs from a chat: tool calls, the agentic loop, creating and editing files |
| 3 | [Learning with an agent: sessions, AGENTS.md and learning mode](03-learning-with-an-agent.md) | Why the agent should explain; what a session is and when to start a new one; what AGENTS.md is |
| 4 | [Exercise 2: twenty years of weather in Katowice](04-meteo.md) | Learning mode, a plan in a file, @ for files, checking messy data, CSV versus Parquet |
| 5 | [Exercise 3: what the agent may and may not do](05-limits.md) | Rules for the model (`AGENTS.md`) versus permissions for the app (`opencode.json`): allow, ask, deny, and their limits |
| 6 | [Exercise 4: files outside the project folder](06-outside-folder.md) | What the agent can reach outside the project; read-only access to a data folder; keeping private files out of reach |

## Before you start

Go through [1. Getting started](../01-getting-started/README.md) in order. You need:

- the opencode app installed ([2. Install the opencode app](../01-getting-started/02-install-opencode.md));
- opencode configured for llmlab: the address ([part 1](../01-getting-started/03-configure-llmlab-endpoint.md)) and
  your API key ([part 2](../01-getting-started/04-configure-llmlab-api-key.md));
- Python installed ([Before the exercises](00-prerequisites.md)).

Do the exercises in order. Each one runs in its own new project folder
([how to create one](01-project-folder.md)).

Screenshots for these pages are in [images/](images/README.md). Files that the
exercises download are in [meteo/](meteo/README.md) (Exercise 2) and
[limits/](limits/README.md) (Exercise 3) and [outside/](outside/README.md)
(Exercise 4).

[← Back to the main README](../README.md)
