# PitRelay for Claude Code

PitRelay as **one Claude Code plugin**, with the **fixed PitRelay engine (ENGINE-FIXES-1)** built in and **PitRelay Collector** included. The collector gets the logs from the robot and the Driver Station, and PitRelay analyzes them inside Claude Code.

## Download

**[pitrelay-claude-code.zip](https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip)** (4.0 MB)

SHA-256: `ed462bed083f66d441f028c0af8be2609abafd6bd2cc9c43ce447ad634305308` (also in [SHA256SUMS.txt](SHA256SUMS.txt))

Version `pitrelay-claude-code-20261024-engine-fixes-1`. Engine `pitrelay-0.1.0-dev-20261024-engine-fixes-1`, JAR SHA-256 `a10fb14d…6b16f7`. Plugin commit `75c12bd`.

## Install

You need **Claude Code**, **Java 17+** and **Python 3.10+**. On a robotics laptop, Java usually comes with WPILib, and the installer finds it. [INSTALL.md](INSTALL.md#requirements) says how to get each.

1. Download the ZIP. You do not need to unzip it.
2. Run it with Python in a terminal:
   * macOS, Linux: `python3 ~/Downloads/pitrelay-claude-code.zip`
   * Windows: `py -3 "%USERPROFILE%\Downloads\pitrelay-claude-code.zip"`
3. Type `accept` for the PitRelay license, then `y` to add PitRelay to Claude Code.
4. Start a new Claude Code session and run `/pitrelay:start`, then `/pitrelay:collect`.

Or download and install in one line:

```sh
curl -fLO https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip && python3 pitrelay-claude-code.zip
```

```powershell
curl.exe -fLO https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip; py -3 pitrelay-claude-code.zip
```

The first line is for macOS and Linux, the second for Windows PowerShell. Both work while this repository is public. If it is private, download the ZIP in a browser where you are signed in.

Later, with the same file:
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
