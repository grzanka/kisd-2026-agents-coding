# PLGrid Forge (llmlab) — reference for opencode

Sources:
- <https://guide.plgrid.pl/en/integrated-platforms/plgrid_forge> (official guide)
- <https://github.com/groundnuty/plgrid-llmlab-opencode> (community opencode setup, unofficial; measurements dated 2026-09/10)

Facts below are copied from those sources. Re-check the model list before each lecture, because PLGrid adds and retires models.

## What it is

PLGrid Forge is an inference service on PLGrid supercomputers (ACK Cyfronet). Model instances run on the supercomputer, and the service is hosted at <https://llmlab.plgrid.pl>.

- It is **OpenAI-API compatible**, so opencode and any OpenAI client can use it.
- It serves chat/text models, embedding models and vision-language models.
- Usage is billed in **credits** from a PLGrid **grant**. Generated tokens are converted to credits, and the cost per 1M tokens depends on model size.
- Privacy: PLGrid administrators cannot see request or response content, and no data leaves the inference servers.

## Key facts

| Item | Value |
|---|---|
| Web UI | `https://llmlab.plgrid.pl` |
| Base URL (OpenAI-compatible) | `https://llmlab.plgrid.pl/api/v1` |
| Chat completions | `https://llmlab.plgrid.pl/api/v1/chat/completions` |
| Embeddings | `https://llmlab.plgrid.pl/api/v1/embeddings` |
| List models (OpenAI format) | `https://llmlab.plgrid.pl/api/v1/models` |
| List models (PLGrid format: status, credit cost, tags, `function_calling_supported`, `is_active`) | `https://llmlab.plgrid.pl/api/v1/models-plgrid-format` |
| Model health | `https://llmlab.plgrid.pl/api/health` (optional `?model=<model_name>`) |
| Create API key with a management key | `https://llmlab.plgrid.pl/api/v1/management/api_keys/create` |
| Auth header | `Authorization: Bearer <API_key>` |
| Other required header | `accept: application/json` |
| Service activation | `https://portal.plgrid.pl/services/111` |

## Getting access (four steps)

1. Create a PLGrid account, get an affiliation and create a team (see the PLGrid Beginner's Guide).
2. Get a **grant** and apply for LLM credits in the Resources tab. Alternatively, renegotiate resources on an existing grant.
3. Activate the service at <https://portal.plgrid.pl/services/111>.
4. Open <https://llmlab.plgrid.pl>, go to **Grants → Generate API Key**, and copy the key.

## API keys

- A key authenticates requests. Whoever has the key can use your credits, with or without a PLGrid account, so treat it like a password.
- The key is shown **only once**, at creation. Forge does not store it.
- When creating a key:
  - The name must be unique within the grant. It is the only mandatory field.
  - The expiration date **cannot be changed later**.
  - Set a **monthly credit limit**. This limits the damage if the key leaks.
  - A total credit limit is optional.
- Each team member sees and manages only their own keys.
- Key statuses are active, expired and revoked. Keys can be archived, which blocks API access immediately.
- **Management keys** are created only in the web UI. They can create API keys through the management endpoint, but cannot be used for inference. They have no usage limits, so never share them. We do not need them for the lectures.

## Models (tags)

| Tag | Meaning |
|---|---|
| Active / Inactive | Whether queries can be sent now |
| Accessible / Inaccessible | Whether one of **your** grants can use it. Access is requested through the PLGrid Helpdesk |
| Non-commercial | Not allowed for users with a commercial affiliation |
| FC | Supports function calling (needed for agentic coding) |
| EMB | Embedding model, not for chat |

The model name used in the API is the full identifier, for example `deepseek-ai/DeepSeek-V4.1-Flash`.

## DeepSeek V4.1 specifics

From the community repo (it measured the live gateway; these values are not from the official guide):

| Item | Value |
|---|---|
| API model id | `deepseek-ai/DeepSeek-V4.1-Flash` |
| opencode model id | `plgrid/deepseek-ai/DeepSeek-V4.1-Flash` |
| Context window | 1,048,576 tokens (1M) |
| Output limit set in the plugin | 32,768 tokens |
| Function calling | yes |
| Reasoning | yes. The model returns reasoning in the `reasoning` field |
| Benchmark | 22/22 correct in all 3 runs (the repo's agentic fixtures, not ours) |
| Flags | non-commercial, and **restricted to some grants** |

- A key whose grant cannot use the model gets the error *"not available for grant 'N'"*. Access is requested through the PLGrid Helpdesk.
- **Before the lectures, check that the grant used for the course can reach this model.**
- Fallback open to all grants: `plgrid/Qwen/Qwen3.6-27B`. It is slower but correct on the benchmark.
- Backup DeepSeek model: `deepseek-ai/DeepSeek-V4.1-Flash-1017` (opencode model id `plgrid/deepseek-ai/DeepSeek-V4.1-Flash-1017`). Its context window, reasoning, function calling and grant access have **not been checked**. Verify with the health check below before relying on it.
- Neighbouring models are `deepseek-ai/DeepSeek-V4-Flash` (700k context, no reasoning) and `deepseek-ai/DeepSeek-V4-Flash-0731`.

## Using it from opencode

The community repo provides a provider plugin (`.opencode/plugins/plgrid.js`), an `opencode.json` and an `AGENTS.md`. The plugin hardcodes the base URL `https://llmlab.plgrid.pl/api/v1`, the model list and per-model limits.

- Setup is copying three things into the project: `.opencode/`, `AGENTS.md` and `opencode.json`. Or put the plugin and config in `~/.config/opencode/`.
- Log in once. The key is stored under the home directory, never in the repo.
  - opencode 1.x: `opencode providers login -p plgrid`
  - opencode 2.x: `opencode auth login plgrid`
- `opencode.json` sets `"model": "plgrid/deepseek-ai/DeepSeek-V4.1-Flash"` and `"small_model": "plgrid/google/gemma-4-31B"`.
- Check with `opencode models plgrid` (1.x). It should list 20 models.
- opencode 1.x and 2.x differ in install command, login command and LSP support. 2.x runs no language servers, and per-agent temperature is ignored.
- Only the terminal TUI was tested by the repo author, not the desktop or web apps.
- The repo defines agents `architect`, `chat`, `researcher`, `reviewer` and `fastfix`, and commands `/check`, `/review` and `/explain`.
- Models without function calling (6 of the 20) need the `chat` agent. The default `build` and `plan` agents send a tools array, and those models reject it with HTTP 400.

## Pitfalls to teach (from the repo's research)

- **Windows:** the repo's instructions are written for bash. Our lectures need PowerShell equivalents, and these are untested.
- **Agent loops:** opencode's `steps` defaults to unlimited, so set a cap per agent.
- **Custom agent prompts:** a custom agent `prompt` replaces the base system prompt instead of appending to it.
- **Silent failures:** `opencode run` can exit 0 having done nothing. Check the result, not the exit code.
- **Weak models:** some models produce green tests over broken code (`Qwen3.6-35B-A3B`, `Llama-3.3-70B`), or report passes they never achieved (`Bielik-11B`). Teach students to verify.
- **Credits:** an agent loop burns tokens. Set a monthly limit on every key.
- **Docs bug:** the official health-check example sends `Bearer {apikey}{apikey}`. That is a typo, so use the key once.
- **Time-sensitive data:** the model list, restrictions and benchmark numbers are a snapshot.

## Quick checks (one command per block)

List the models your key can see:

```bash
curl -H "Authorization: Bearer $LLMLAB_API_KEY" https://llmlab.plgrid.pl/api/v1/models-plgrid-format
```

Check one model's health:

```bash
curl -H "Authorization: Bearer $LLMLAB_API_KEY" "https://llmlab.plgrid.pl/api/health?model=deepseek-ai/DeepSeek-V4.1-Flash"
```

## Open questions for the course

- Which grant do students use? One shared grant with per-student keys, or each student's own grant? Is DeepSeek V4.1 Flash enabled for it?
- What are the credit budget and per-key monthly limit for 20 or more students?
- Windows setup for opencode: native, or WSL? This is untested.
