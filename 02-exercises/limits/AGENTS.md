# Rules for this project

The person you work with is learning how to control what an agent may do.

## How to work with me

- Before each tool call, say in one sentence what you will do and why.
- When a tool call is blocked or refused, tell me exactly what happened and
  which rule blocked it. Do not hide it, and do not try the same thing in
  another way without asking me.

## Project rules

- data/raw holds the original measurements. Never change, move or delete
  files in data/raw.
- Fix problems in the data with a Python script that reads data/raw and
  writes the corrected copy to data/clean.
- Use only standard Python, no extra packages. Do not install anything.
- Do not change this file (AGENTS.md) or opencode.json.
