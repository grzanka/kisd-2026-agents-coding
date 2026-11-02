# 3. Get access to llmlab (PLGrid Forge)

PLGrid Forge is a service that runs large language models on the PLGrid
supercomputers at ACK Cyfronet. It is hosted at <https://llmlab.plgrid.pl>.
opencode can use it as its model provider, so your prompts and data are processed
in Poland, on PLGrid hardware, and not by a commercial cloud.

- Usage is paid in **credits** from a PLGrid **grant**. The more text the model
  reads and writes, the more credits it uses.
- PLGrid administrators cannot see your prompts or the answers.

## Get an API key

1. Have a PLGrid account and be a member of a team with a **grant** that has LLM
   credits.
2. Activate the service at <https://portal.plgrid.pl/services/111>.
3. Open <https://llmlab.plgrid.pl>, go to **Grants → Generate API Key**.

When you create the key:

- Give it any unique name.
- Set a **monthly credit limit**. An agent can use many credits quickly, and the
  limit protects you if the key leaks.
- Copy the key right away. It is shown **only once**. Keep it until you finish
  [4. Connect opencode to llmlab](04-connect-opencode-to-llmlab.md).

<details>
<summary>Explain: what is an API key?</summary>

An API key is a long secret string that lets a program (here, opencode) use the
service on your behalf. Anyone who has your key can spend your credits, so treat
it like a password: do not paste it into files, emails or chats.

</details>

## Sources

- PLGrid Forge official guide:
  <https://guide.plgrid.pl/en/integrated-platforms/plgrid_forge>

The model list and access rules change over time. Check
<https://llmlab.plgrid.pl> for the current state.

---

← Previous: [2. Install the opencode app](02-install-opencode.md) · [Docs overview](README.md) · Next: [4. Connect opencode to llmlab →](04-connect-opencode-to-llmlab.md)
