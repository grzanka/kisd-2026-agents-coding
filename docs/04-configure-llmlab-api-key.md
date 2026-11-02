# 4. Configure opencode for llmlab, part 2: the API key

After [part 1](03-configure-llmlab-endpoint.md), opencode knows where llmlab is.
Now it needs your **API key**, so that llmlab lets it in and bills your grant.

llmlab (PLGrid Forge) runs large language models on the PLGrid supercomputers
at ACK Cyfronet, <https://llmlab.plgrid.pl>. Your prompts and data are processed
in Poland, on PLGrid hardware, and not by a commercial cloud.

- Usage is paid in **credits** from a PLGrid **grant**. The more text the model
  reads and writes, the more credits it uses.
- PLGrid administrators cannot see your prompts or the answers.

## An API key, never your password

> [!CAUTION]
> Never give opencode, or any program, script or settings file, your **PLGrid
> password**. Use an **API key**, made only for this purpose.

An **API key** is a long secret string that lets a program (here, opencode) use
one service on your behalf. It is not the same as your password:

| | Your PLGrid password | An API key |
|---|---|---|
| What it opens | Everything: the PLGrid portal, your grants, the supercomputers, your files there | One service only: llmlab |
| How many | One | As many as you like: one per project, per computer, per course |
| How long it works | Until you change it | Until the expiry date you set |
| Spending limit | None | A credit quota you set |
| If it leaks | Someone controls your whole account, in your name; you change the password everywhere | You archive that one key in a minute; the others keep working |

Why this matters with an agent: an agent reads and writes many files, and its
settings and notes get copied, zipped, sent to colleagues or put on GitHub.
Sooner or later a secret in such a file ends up where it should not be. A leaked
key costs at most its credit quota. A leaked password can cost your account, your
grant and your data.

Saving your password once would be quicker than making keys. Take the extra five
minutes anyway: it is the cheapest insurance you will ever buy.

Also: never paste the key into the opencode chat. Everything you type there is
sent to the model.

## Step 1. Get an API key

You need a PLGrid account and membership in a team with a **grant** that has LLM
credits. Ask the course organisers which grant to use.

### 1a. Activate the llmlab service

Go to <https://portal.plgrid.pl/services/111> and log in with your PLGrid
account. This is the service *Access to Large Language Models at the Cyfronet
centre [grant required]*. Click **Activate**.

Expected result: the green label **Active**. Activation may take a while; if
you still see **Activate**, try again later. Do not click **Deactivate**.

<img src="images/04-key-1-portal-service.png" alt="PLGrid portal: the service Access to Large Language Models at the Cyfronet centre, marked Active" width="700">

### 1b. Log in to llmlab

Go to <https://llmlab.plgrid.pl> and click **Log in to the service**. Log in
with your PLGrid account.

<img src="images/04-key-2-llmlab-home.png" alt="LLM-Lab home page with the button Log in to the service and the list of available models" width="700">

### 1c. Choose your grant

You land on **Grants**, the list of your active grants. Each shows the credits
used and the total, here 6,052 of 10,000. Click **Generate API Key** next to the
course grant.

<img src="images/04-key-3-grants.png" alt="LLM Lab Grants page: grant plgtraining2026 with credits and the Generate API Key button" width="700">

On the next page, **Actions**, click **Generate**.

<img src="images/04-key-4-grant-actions.png" alt="Grant actions page with Generate API Key and the Generate button" width="700">

### 1d. Fill in the form

![Generate API Key form: Name, Expires, Monthly Credits Quota, Total Credits Quota, All grant models](images/04-key-5-generate-form.png)

| Field | What to enter |
|---|---|
| **Name** | What the key is for, for example `kisd course, my laptop`. You will see it in the list of keys. |
| **Expires** | A date soon after the course ends. An empty field means the key never expires: avoid that. |
| **Monthly Credits Quota** | The most this key may spend per month. An agent can use many credits quickly; the quota protects you if the key leaks or a session runs away. |
| **Total Credits Quota** | The most this key may spend in total. Optional. |
| **All grant models** | Leave it ticked. |

Click **Create**.

### 1e. Copy the key now: it is shown only once

> [!IMPORTANT]
> The key is shown **only once**, right after you click **Create**. When you
> leave or reload the page, it is gone, and nobody can show it to you again,
> not even the administrators.

Click the copy button on the right of the key, and go straight on to step 2.

![The new key under "Your API Token", with a copy button; most of the key is hidden in this picture](images/04-key-6-key-shown.png)

Your key starts with `plg-` and is about 45 characters long. In the picture
above most of it is hidden: never use a key from a picture, page or file you
did not create yourself.

If you lost the key before you used it, nothing is broken: generate a new one,
and archive the old one in **API Keys** (step 1f).

### 1f. Your keys in the list

Under **API Keys → Active Keys** you see all your keys: name, grant, the first
characters of the key, the expiry date and the models it can use. The full key is
not shown here. The **⋮** menu on the right of a key lets you archive it, so it
stops working.

<img src="images/04-key-7-key-list.png" alt="LLM Lab API Keys page: the key llmlab python lecture, Active, grant plgtraining2026, expiring 2026-11-30, all models" width="700">

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
