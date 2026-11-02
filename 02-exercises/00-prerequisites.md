# Before the exercises: check that Python is installed

In the exercises the agent writes small **Python** programs and runs them on your
computer. For that, Python must be installed. You do not need to know Python;
the agent writes the code.

Do this once, before the first exercise. It takes 2 minutes if Python is already
there, and about 10 minutes if you need to install it.

<!-- TODO: add screenshots of the expected output (python --version, pip --version, Python not found). -->

## Step 1. Open PowerShell

Press the **Windows** key, type `PowerShell`, and press **Enter**.

## Step 2. Check Python

```powershell
python --version
```

Expected result: one line with the version, for example `Python 3.13.5`. Any
version from **3.10** up is fine. Go on with step 3.

If instead you see one of these, Python is missing. Go to
[Install Python](#install-python):

- *Python was not found; run without arguments to install from the Microsoft
  Store...*
- *python : The term 'python' is not recognized...*
- the **Microsoft Store** opens on a Python page.

<details>
<summary>Explain: why does Windows open the Microsoft Store?</summary>

Windows has a placeholder called `python` that only points to the Microsoft
Store. It is not Python itself. When you install Python properly (below), the
real `python` takes over.

</details>

## Step 3. Check pip

pip is the tool that installs extra Python packages, for example for plots. The
agent uses it in the exercises.

```powershell
python -m pip --version
```

Expected result: one line that starts with `pip` and a version number, for
example `pip 25.1 from C:\Users\yourname\...`.

If both steps worked, you are ready: go to
[Exercise 1](01-pendulum.md).

## Install Python

Install it with **winget**, the program installer built into Windows 10 and 11:

```powershell
winget install -e --id Python.Python.3.13
```

Expected result: a download progress bar, then *Successfully installed*. If
Windows asks *Do you want to allow this app to make changes to your device?*,
click **Yes**. If winget asks you to accept the source agreements, type `Y` and
press **Enter**.

Then **close PowerShell and open a new one**. A window that was already open
does not know about the new program. Repeat steps 2 and 3.

If opencode was open, close it and start it again too, for the same reason.

<details>
<summary>No winget? Install from python.org instead</summary>

If you see *winget is not recognized*, download the installer from
<https://www.python.org/downloads/> and run it. On the first screen, tick
**Add python.exe to PATH** before you click **Install Now**. Without that tick,
`python --version` still fails.

</details>

<details>
<summary>Linux / macOS</summary>

Use `python3` instead of `python`:

```bash
python3 --version
```

```bash
python3 -m pip --version
```

To install: on Ubuntu or Debian, install the packages `python3`, `python3-pip`
and `python3-venv` with `sudo apt install`. On macOS, install
[Homebrew](https://brew.sh), then run `brew install python`.

</details>

<details>
<summary>Errors</summary>

- `python --version` still fails after installing: you did not open a **new**
  PowerShell window. Close all of them and open a new one.
- *No module named pip*: run `python -m ensurepip`, then step 3 again.
- You are on a university or company computer and cannot install programs: ask
  your IT department to install Python 3, or use a personal computer for the
  exercises.

</details>

---

← Previous: [5. First steps in opencode](../01-getting-started/05-first-steps.md) · [Overview](README.md) · Next: [Exercise 1 →](01-pendulum.md)
