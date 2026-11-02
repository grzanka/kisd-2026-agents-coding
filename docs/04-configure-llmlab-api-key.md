# 4. Configure opencode for llmlab, part 2: the API key

After [part 1](03-configure-llmlab-endpoint.md), opencode knows where llmlab is.
Now it needs your **API key**, so that llmlab lets it in and bills your grant.

llmlab (PLGrid Forge) runs large language models on the PLGrid supercomputers
at ACK Cyfronet, <https://llmlab.plgrid.pl>. Your prompts and data are processed
in Poland, on PLGrid hardware, and not by a commercial cloud.

- Usage is paid in **credits** from a PLGrid **grant**. The more text the model
  reads and writes, the more credits it uses.
- PLGrid administrators cannot see your prompts or the answers.

## Step 1. Get an API key

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

## Step 2. Give the key to opencode

opencode looks for the key in two places. Use **one** of them.

<!-- TODO: option A not yet tested in the desktop app; add screenshots. -->

### Option A: paste the key in the opencode app

1. In opencode, click the model name under the text box, then **See 70+ more
   providers**.
2. In the **Integrations** search box, type `plgrid`.
3. Click **PLGrid Forge (Cyfronet)**, choose **API key**, paste your key, and
   confirm.

Expected result: the model name under the text box changes to
**DeepSeek V4.1 Flash (PLGrid)**. Go on with step 3; you do not need to restart.

If the app does not offer to paste a key, use option B.

### Option B: save the key as a Windows setting

Close opencode first. In PowerShell, run the command below. Replace
`paste-your-key-here` with your key and keep the quotes:

```powershell
setx PLGRID_API_KEY "paste-your-key-here"
```

Expected result:

```text
SUCCESS: Specified value was saved.
```

This saves the key as a Windows setting for your user account (an
*environment variable*). Programs started from now on can read it; programs
already open cannot. That is why opencode must be closed during this step.

<details>
<summary>Linux / macOS</summary>

Add this line to `~/.bashrc` or `~/.zshrc`, then open a new terminal:

```bash
export PLGRID_API_KEY="paste-your-key-here"
```

</details>

## Step 3. Check that it works

If opencode is closed, start it: press the **Windows** key, type `opencode`, and
press **Enter**.

Expected result: under the text box you see **DeepSeek V4.1 Flash (PLGrid)**.
If a free model is still selected, click its name, type `plgr`, and pick
**DeepSeek V4.1 Flash (PLGrid)**, as in
[part 1, step 5](03-configure-llmlab-endpoint.md#step-5-check-that-opencode-sees-llmlab).

> [!IMPORTANT]
> The model list also contains a free **DeepSeek V4.1 Flash** run by opencode,
> not by PLGrid. Use only the one that ends with **(PLGrid)**.

Type a short question, such as `What is 2 + 2?`, and send it. You should get
an answer within a few seconds.

<details>
<summary>Errors</summary>

- A free model is still selected and there is no **(PLGrid)** model: opencode
  did not read the settings file. Go back to
  [part 1](03-configure-llmlab-endpoint.md), step 4.
- *401* or *Unauthorized*: the key is wrong, or (option B) opencode was open
  during step 2. Repeat step 2, then restart opencode. On some computers you
  need to sign out of Windows and sign in again.
- *not available for grant*: your grant cannot use DeepSeek. Click the model
  name under the text box and pick **Gemma 4 31B (PLGrid)**. See
  [Which model](05-first-steps.md#which-model).

</details>

## Summary: what is now on your computer

The settings and the key are kept in different places. `yourname` stands for your
Windows user name.

| What | Where | Created by |
|---|---|---|
| Settings: llmlab address and models | `C:\Users\yourname\.config\opencode\opencode.json` | Part 1 |
| API key, option A | A file in `C:\Users\yourname\.local\share\opencode\`: `auth.json` or `opencode.db`, depending on the opencode version | opencode, in step 2 |
| API key, option B | Not a file: a Windows setting (environment variable) `PLGRID_API_KEY` of your user account | `setx` in step 2 |

The settings file has no secrets in it. The key is stored as plain text, so do
not share the `.local` folder or send its files to anyone.

You will not normally see these folders. File Explorer opens on *This PC*,
*Desktop* or *Documents*, and these folders sit one level up, directly in your
user folder. Their names start with a dot, which is the Linux and macOS way of
marking hidden folders; there they are hidden, and that habit came to Windows
with programs like opencode.

To open the settings folder in File Explorer, press **Windows+R**, paste this,
and press **Enter**:

```text
%USERPROFILE%\.config\opencode
```

<details>
<summary>Linux / macOS</summary>

The same paths, starting with `~` instead of `C:\Users\yourname`:
`~/.config/opencode/opencode.json` and `~/.local/share/opencode/`. Folders
starting with a dot are hidden; list them with `ls -a`, or press
**Cmd+Shift+.** in the macOS Finder. Option B lives in `~/.bashrc` or
`~/.zshrc`.

</details>

<details>
<summary>Remove everything</summary>

Delete the folder `C:\Users\yourname\.config\opencode`, and the folder
`C:\Users\yourname\.local\share\opencode`. The second one also holds your
saved opencode sessions, so they are gone too. If you used option B, remove the
setting:

```powershell
[Environment]::SetEnvironmentVariable("PLGRID_API_KEY", $null, "User")
```

</details>

## Sources

- PLGrid Forge official guide:
  <https://guide.plgrid.pl/en/integrated-platforms/plgrid_forge>

The model list and access rules change over time. Check
<https://llmlab.plgrid.pl> for the current state.

---

← Previous: [3. Configure opencode for llmlab, part 1: the address](03-configure-llmlab-endpoint.md) · [Docs overview](README.md) · Next: [5. First steps in opencode →](05-first-steps.md)
