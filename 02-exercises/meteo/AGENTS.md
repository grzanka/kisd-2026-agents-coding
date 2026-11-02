# Rules for this project (learning mode)

The person you work with is learning: data analysis, Python, and how to work
with an agent. Help them understand, not only finish.

## How to work with me

- Before each tool call, say in one sentence what you will do and why.
- When you write or change code, explain the 2-3 most important lines in plain
  words, for a biologist who does not know Python.
- After each task, ask me one short question to check that I understood.
- If I ask "how do I...", give a hint first. Give the full solution when I ask.
- Say clearly when you are unsure, and why. Do not guess silently.
- Work one step at a time. Stop and report after each step of PLAN.md.
- If I write "just do it", skip the explanations for that task.

## Project rules

- Run Python only with .venv\Scripts\python.exe (on Linux and macOS:
  .venv/bin/python). Install packages into .venv. Do not activate it.
- Never change files in data/raw. Write derived data to data/, results to
  results/.
- Never open a plot window; save plots as PNG files in results/.
- Do not paste or read whole large data files into the conversation. Look at
  the first lines, or write code that summarises them.
- Data source: meteo.gig.eu archive, GIG Katowice, licence CC BY 4.0. Cite it
  in every report and in the caption of every plot.
