# PLGrid Forge (llmlab) — using it from opencode

PLGrid Forge is a service that runs large language models on the PLGrid
supercomputers at ACK Cyfronet. It is hosted at <https://llmlab.plgrid.pl>.
opencode can use it as its model provider, so your prompts and data are processed
in Poland, on PLGrid hardware, and not by a commercial cloud.

- Usage is paid in **credits** from a PLGrid **grant**. The more text the model
  reads and writes, the more credits it uses.
- PLGrid administrators cannot see your prompts or the answers.

## 1. Get access

1. Have a PLGrid account and be a member of a team with a **grant** that has LLM
   credits.
2. Activate the service at <https://portal.plgrid.pl/services/111>.
3. Open <https://llmlab.plgrid.pl>, go to **Grants → Generate API Key**.

When you create the key:

- Give it any unique name.
- Set a **monthly credit limit**. An agent can use many credits quickly, and the
  limit protects you if the key leaks.
- Copy the key right away. It is shown **only once**.

<details>
<summary>Explain: what is an API key?</summary>

An API key is a long secret string that lets a program (here, opencode) use the
service on your behalf. Anyone who has your key can spend your credits, so treat
it like a password: do not paste it into files, emails or chats.

</details>

## 2. Connect opencode

We use the community setup from
[groundnuty/plgrid-llmlab-opencode](https://github.com/groundnuty/plgrid-llmlab-opencode).
Copy these three items from that repository into your project folder:

- the `.opencode` folder
- `AGENTS.md`
- `opencode.json`

Then, in PowerShell inside your project folder, log in once and paste your API key
when asked. The key is stored in your user folder, not in the project.

```powershell
opencode auth login plgrid
```

<details>
<summary>Using opencode 1.x?</summary>

The login command is different:

```powershell
opencode providers login -p plgrid
```

</details>

## 3. Which model

The setup uses this model by default:

| Model (as written in opencode) | Notes |
|---|---|
| `plgrid/deepseek-ai/DeepSeek-V4.1-Flash` | Default. Fast, large context, good at agentic work. **Only some grants can use it.** |
| `plgrid/Qwen/Qwen3.6-27B` | Fallback that every grant can use. Slower, but reliable. |

If you see the error *"not available for grant 'N'"*, your grant cannot use the
default model. Switch to the fallback, or ask the PLGrid Helpdesk for access.

Only use models marked **FC** (function calling) on <https://llmlab.plgrid.pl>.
An agent needs function calling to run commands and edit files.

## 4. Check that it works

Start opencode in your project folder:

```powershell
opencode
```

Expected result: the opencode screen opens and shows the model name at the
bottom. Type a short question, such as "What files are in this folder?", and
you should get an answer within a few seconds.

<details>
<summary>Going further: test the key without opencode</summary>

Put your key in a variable for this PowerShell session only:

```powershell
$env:LLMLAB_API_KEY = "paste-your-key-here"
```

List the models your key can use:

```powershell
curl.exe -H "Authorization: Bearer $env:LLMLAB_API_KEY" https://llmlab.plgrid.pl/api/v1/models
```

Expected result: a block of JSON text with model names. An error saying
`401` or `Unauthorized` means the key is wrong or expired.

Use `curl.exe`, not `curl`. In Windows PowerShell, `curl` is a different command.

</details>

## 5. Good habits

- **Check the results yourself.** Models can write code that runs but computes the
  wrong thing, or claim that tests pass when they do not. Look at the numbers and
  plots before you trust them.
- **Watch your credits.** Long agent sessions use many credits. Keep the monthly
  limit on your key.
- **A quiet finish is not a success.** If opencode stops without an error, check
  that the expected files or results actually exist.

## Sources

- PLGrid Forge official guide:
  <https://guide.plgrid.pl/en/integrated-platforms/plgrid_forge>
- Community opencode setup (unofficial, checked 2026-09/10):
  <https://github.com/groundnuty/plgrid-llmlab-opencode>

The model list and access rules change over time. Check
<https://llmlab.plgrid.pl> for the current state.
