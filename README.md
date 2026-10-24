# PitRelay for Claude Code

PitRelay as **one Claude Code plugin**, with the **fixed PitRelay engine (ENGINE-FIXES-1)** built in and **PitRelay Collector** included. The collector gets the logs from the robot and the Driver Station, and PitRelay analyzes them inside Claude Code.

## Download

**[pitrelay-claude-code.zip](https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip)** (4.0 MB)

SHA-256: `f37b63f0cc2c985313d416ef4004184fc28fc0985a9fc361c9384bac99d1e817` (also in [SHA256SUMS.txt](SHA256SUMS.txt))

Version `pitrelay-claude-code-20261024-engine-fixes-1`. Engine `pitrelay-0.1.0-dev-20261024-engine-fixes-1`, JAR SHA-256 `a10fb14d…6b16f7`. Plugin commit `75c12bd`.

## Install

You need **Claude Code** (the command line or the desktop app), **Java 17+** and **Python 3.10+**. On a robotics laptop, Java usually comes with WPILib, and the installer finds it. [INSTALL.md](INSTALL.md#requirements) says how to get each.

1. Download the ZIP. You do not need to unzip it.
2. Run it with Python in a terminal:
   * macOS, Linux: `python3 ~/Downloads/pitrelay-claude-code.zip` (if Safari unzipped it into a folder by itself: `python3 ~/Downloads/pitrelay-claude-code/install.py`)
   * Windows PowerShell: `py "$env:USERPROFILE\Downloads\pitrelay-claude-code.zip"` (Command Prompt: `py "%USERPROFILE%\Downloads\pitrelay-claude-code.zip"`)
3. Type `accept` for the PitRelay license, then `y` to add PitRelay to Claude Code.
4. Start a new Claude Code session and run `/pitrelay:start`, then `/pitrelay:collect`.

Or download and install in one line:

```sh
curl -fLO https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip && python3 pitrelay-claude-code.zip
```

```powershell
curl.exe -fLO https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip; if ($?) { py pitrelay-claude-code.zip }
```

The first line is for macOS and Linux, the second for Windows PowerShell. Both work while this repository is public. If it is private, download the ZIP in a browser where you are signed in.

Later, with the same file:
* `doctor` shows the Python, Java and Claude Code the installer finds, if something does not work.
* `verify` checks the installation.
* A newer ZIP upgrades it and keeps your data.
* `uninstall` removes it from Claude Code.

[INSTALL.md](INSTALL.md) has everything else: what the installer does, options, offline install, troubleshooting, installing by hand, what the fixed engine changes, and what was and was not tested.

## Status

* **The engine is a candidate, not a PitRelay release:** the v11 source with ENGINE-FIXES-1. It adds:
  * REVLOG 2.x decoding;
  * Driver Station files read with the wrong adapter now name the right one;
  * `transfer_id`, and uploads that do not use workflow budgets;
  * CSV time-unit statuses.
* **Tested on Linux:** installing from this ZIP with Python 3.10 to 3.13, verify, the server as Claude Code starts it, collection and analysis, upgrade and uninstall.
* **Not tested:** Windows, macOS, the desktop app, a live Claude session, and a real robot or real logs.
