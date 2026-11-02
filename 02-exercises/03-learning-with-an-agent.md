# Learning with an agent: sessions, AGENTS.md and learning mode

In [Exercise 1](02-pendulum.md) you watched what an agent can do. From now on
we change roles: **the agent explains, you learn.** This page explains why, and
the three things you need for it: sessions, the file `AGENTS.md`, and learning
mode.

Reading time: about 10 minutes.

## Why "explain it to me" instead of "do it for me"

An agent can do a whole analysis while you watch. That is fast, but you learn
almost nothing, and you cannot check what you do not understand. Studies of
students and programmers using AI point the same way:

- Students who used a chatbot freely did better while practising, and **worse
  in the exam** afterwards, without the chatbot. Students whose chatbot gave
  hints instead of answers did not have this problem
  ([Bastani et al., PNAS 2025](https://papers.ssrn.com/abstract=4895486)).
- Programmers learning a new tool with AI help understood it less well
  afterwards. The ones who **only delegated** learned least; the ones who
  **asked the AI to explain** kept their learning
  ([Anthropic, 2026](https://infoq.com/news/2026/02/ai-coding-skill-formation/)).
- Beginners who struggle can get an **illusion of competence**: the code runs,
  so they think they understand it
  ([Prather et al., 2024](https://arxiv.org/abs/2405.17739)).

So in the next exercises you will ask the agent to explain, you will **predict**
results before the agent runs anything, and you will **change** some things
yourself.

## What is a session

A **session** is one conversation with the agent. Each session has its own
name, and opencode keeps a list of them for each project. A session holds:

- your prompts;
- the agent's answers and Thoughts;
- every tool call and its result.

**What a session remembers:** everything said and done in it. With every new
prompt, the **whole session so far** is sent to the model again. You saw this
in [Exercise 1, step 12](02-pendulum.md#step-12-how-big-was-this-session): the
input was hundreds of times larger than the output.

**What a session does not remember:** anything from other sessions. A new
session starts from zero. It knows only what is in `AGENTS.md` (below) and what
it reads from your files.

**What stays when a session ends:** your files. That is why everything important
goes into files: the plan, notes, scripts, results. Not only into the chat.

### When to start a new session

Start a new session (the **+** at the top, or **Ctrl+T**) when:

- you start a new task, or a new phase of the same work;
- the session is long, and each answer gets slower;
- the agent goes in circles, repeats a mistake, or sticks to a wrong idea from
  earlier;
- you changed `AGENTS.md`;
- you took a long side trip that the next step does not need.

**Before you leave a session**, ask the agent to write down what the next
session needs to know, for example in `NOTES.md`. That is the handover, like a
note for a colleague who takes over your lab shift.

<details>
<summary>Explain: why not one long session for everything?</summary>

- **Cost.** Every prompt sends the whole session again. A session ten times
  longer makes every prompt about ten times more expensive in credits.
- **Quality.** A long session is full of old details, wrong attempts and
  corrections. The model has to find what matters now among all of that, and
  sometimes it picks up an old, wrong idea.
- **Order.** One session per task is easy to find again and easy to export as a
  record.

</details>

## What is AGENTS.md

`AGENTS.md` is a plain text file in the main folder of your project. When a
session starts, opencode reads it and sends it to the model with every request,
before your prompt. Write in it what the agent should **always** know about this
project:

- what the project is about;
- the rules, for example "never change the raw data";
- where the data comes from, and how to cite it;
- how to run things, for example which Python to use.

Good to know:

- It is the **memory of the project**. A session forgets everything when it
  ends; `AGENTS.md` stays.
- It is read when a session **starts**. If you change it, start a new session.
- Keep it **short**. It is sent with every request, so every line costs tokens,
  and long lists of rules are followed less well.
- **No secrets** in it: no passwords, no API keys.
- It is a common convention: other agents read `AGENTS.md` too. Claude Code
  uses a file called `CLAUDE.md`; opencode reads that one if there is no
  `AGENTS.md`.
- You can write it yourself, like any text file, or let the agent write a first
  version with the command `/init`.

The difference between `AGENTS.md` and `NOTES.md`: `AGENTS.md` holds the rules
that are always true; `NOTES.md` holds the state of the work, which changes from
session to session.

## Learning mode

For the next exercises we give you a ready `AGENTS.md` that switches the agent
into **learning mode**. It tells the agent to:

- say what it will do, and why, before each tool call;
- explain the most important lines of every piece of code in plain words;
- ask you one short check question after each task;
- give hints first when you ask "how do I...";
- say when it is unsure;
- stop after each step of the plan.

You can read the whole file here: [meteo/AGENTS.md](meteo/AGENTS.md). If you
want the agent to just do one task, write "just do it".

## Prompts that teach you something

Add one of these to your prompts, or send them on their own:

| You want to learn... | Ask |
|---|---|
| ...to prompt better | "Before you start: how would you improve my prompt? Show the better version and say why." |
| ...what the agent will do | "Which tools will you use for this, and why? Do not run anything yet." |
| ...to read Python | "Explain lines 10–20 of this script for a biologist. Then give me one small change to try myself." |
| ...to explore data | "Before any analysis: list 5 questions we should ask about this data, and how to check each." |
| ...good practice | "What could make this result wrong? List the assumptions we made." |
| ...whether you understood | "Ask me 3 questions about what we just did. Tell me if my answers are right." |

---

← Previous: [Exercise 1](02-pendulum.md) · [Overview](README.md) · Next: [Exercise 2 →](04-meteo.md)
