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

We use the **opencode desktop app** with a small settings file from this
repository, [config/opencode.json](../config/opencode.json). It sets the llmlab
address and two models. Your API key stays outside the file: you paste it in
the opencode app, or save it in a Windows setting called `PLGRID_API_KEY`.

Follow [Step by step: connect the opencode app to llmlab](opencode-llmlab-setup.md).

## 3. Which model

| Model (as shown in opencode) | Model id on llmlab | Notes |
|---|---|---|
| DeepSeek V4.1 Flash (PLGrid) | `deepseek-ai/DeepSeek-V4.1-Flash` | Default. Fast, large context, good at agentic work. **Only some grants can use it.** |
| Gemma 4 31B (PLGrid) | `google/gemma-4-31B` | Fallback. Also used for the short session titles. |

If you see the error *"not available for grant 'N'"*, your grant cannot use the
default model. Pick **Gemma 4 31B (PLGrid)** in the model list, or ask the PLGrid
Helpdesk for access.

Only use models marked **FC** (function calling) on <https://llmlab.plgrid.pl>.
An agent needs function calling to run commands and edit files.

## 4. Check that it works

Start the opencode app. Expected result: under the text box you see
**DeepSeek V4.1 Flash (PLGrid)**. Ask a short question, such as
"What is 2 + 2?", and you should get an answer within a few seconds.

<details>
<summary>Going further: test the key without opencode</summary>

If you already did step 4 of the setup guide, open a new PowerShell window and
skip this command. Otherwise, put your key in a variable for this PowerShell
session only:

```powershell
$env:PLGRID_API_KEY = "paste-your-key-here"
```

List the models your key can use:

```powershell
curl.exe -H "Authorization: Bearer $env:PLGRID_API_KEY" https://llmlab.plgrid.pl/api/v1/models
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
- Model limits in our settings file were taken from the community setup
  <https://github.com/groundnuty/plgrid-llmlab-opencode> (unofficial, checked 2026-11).

The model list and access rules change over time. Check
<https://llmlab.plgrid.pl> for the current state.
