# 7. For experts: other ways to set up

Background and alternatives to the main path, for participants who use a
terminal every day. If you followed pages 2–4, you do not need anything here.

## Where opencode keeps your settings and key

The settings and the key are kept in different files. `yourname` stands for your
Windows user name.

| What | Where | Created in |
|---|---|---|
| Settings: llmlab address and models | `C:\Users\yourname\.config\opencode\opencode.json` | [Part 1](03-configure-llmlab-endpoint.md) |
| Your API key | A file in `C:\Users\yourname\.local\share\opencode\`: `auth.json` or `opencode.db`, depending on the opencode version | [Part 2](04-configure-llmlab-api-key.md#step-2-give-the-key-to-opencode) |

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
**Cmd+Shift+.** in the macOS Finder.

</details>

<details>
<summary>Remove everything</summary>

Delete the folder `C:\Users\yourname\.config\opencode`, and the folder
`C:\Users\yourname\.local\share\opencode`. The second one also holds your
saved opencode sessions, so they are gone too. Then archive the key in llmlab
([part 2, step 1f](04-configure-llmlab-api-key.md#1f-your-keys-in-the-list)).

</details>

## Give opencode the key as an environment variable

Instead of pasting the key in the app
([part 2, step 2](04-configure-llmlab-api-key.md#step-2-give-the-key-to-opencode)),
you can store it in the environment variable `PLGRID_API_KEY`. The settings file
from [part 1](03-configure-llmlab-endpoint.md) tells opencode to look there
(`"env": ["PLGRID_API_KEY"]`). This is handy for scripts and the terminal version
of opencode.

Close opencode first: a program reads environment variables only when it
starts. In PowerShell, replace `paste-your-key-here` with your key and keep the
quotes:

```powershell
setx PLGRID_API_KEY "paste-your-key-here"
```

Expected result: `SUCCESS: Specified value was saved.` The variable is saved for
your Windows user account. Programs started from now on can read it; programs
already open cannot. If opencode still answers *Unauthorized*, sign out of
Windows and sign in again.

On Linux or macOS, add this line to `~/.bashrc` or `~/.zshrc`, then open a new
terminal:

```bash
export PLGRID_API_KEY="paste-your-key-here"
```

To remove the variable on Windows again:

```powershell
[Environment]::SetEnvironmentVariable("PLGRID_API_KEY", $null, "User")
```

The key is then stored in the Windows registry (Linux/macOS: in your shell
file) as plain text, just like in opencode's own file. Do not put it in a
project folder or in `opencode.json`.

## Test the key without opencode

Put your key in a variable for this PowerShell session only. Skip this if you
already set it with `setx` above and opened a new PowerShell window:

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

## The terminal version of opencode

The terminal version (the `opencode` command, installed with the commands on
<https://opencode.ai/download>) uses the same settings file and the same models.
It is handy for scripts, for example `opencode run "summarise data.csv"`. It
reads the key from opencode's own file or from `PLGRID_API_KEY`.

---

← Previous: [6. Optional: opencode and Claude Code](06-opencode-vs-claude-code.md) · [Overview](README.md)
