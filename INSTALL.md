# PitRelay for Claude Code: install guide

One file, `pitrelay-claude-code.zip`, installs **PitRelay into Claude Code** as one plugin, with the **fixed PitRelay engine (ENGINE-FIXES-1)** built in and **PitRelay Collector** included. In Claude Code you then have:

* the PitRelay engine and PitRelay's own skills;
* **PitRelay Collector**, which copies logs from the robot (SSH/SFTP, read-only) and from Driver Station folders into a private store on your computer, and hands them to PitRelay;
* the commands `/pitrelay:start`, `/pitrelay:collect`, `/pitrelay:diagnose <symptom>`, `/pitrelay:checkup` and `/pitrelay:import-collection <zip>`.

| | |
|---|---|
| Version | `pitrelay-claude-code-20261024-engine-fixes-1` |
| Engine | `pitrelay-0.1.0-dev-20261024-engine-fixes-1`: the v11 source with ENGINE-FIXES-1, compiled and packaged. A candidate, not a PitRelay release. |
| Engine JAR SHA-256 | `a10fb14d720d46789dbfc4c5fc66ef1048ba329238d35ee7df3900e66a6b16f7` |
| `package-manifest.json` SHA-256 | `e62f01a685dbd1783a1af9211bc49303fdf3fa093ed202e2faf89df2788b5723` |
| PitRelay license SHA-256 | `04bccdf9562964c3f6b9bd030f84a5d504a7c69910355400bfbfd4e3ec3878a4` |
| Plugin | PitRelay for Claude Code, commit `75c12bd` |

## Quick install

**You need:** Claude Code, Java 17 or later and Python 3.10 or later ([details](#requirements)). On a robotics laptop, Java is usually already there: the installer finds the JDK that WPILib installs.

1. **Download** [`pitrelay-claude-code.zip`](https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip). You do not need to unzip it.
2. **Run it with Python** in a terminal (macOS: Terminal; Windows: PowerShell or Command Prompt):

   | | |
   |---|---|
   | macOS, Linux | `python3 ~/Downloads/pitrelay-claude-code.zip` |
   | Windows, PowerShell | `py "$env:USERPROFILE\Downloads\pitrelay-claude-code.zip"` |
   | Windows, Command Prompt | `py "%USERPROFILE%\Downloads\pitrelay-claude-code.zip"` |

3. **Answer two questions:** type `accept` for the PitRelay license, then `y` to add PitRelay to Claude Code.
4. **Start a new Claude Code session** and run `/pitrelay:start`, then `/pitrelay:collect`.

The installer prints its version first, so you can tell which download you ran. Two browser habits to know:

* **Safari** may unzip the download into a folder by itself. Then run `python3 ~/Downloads/pitrelay-claude-code/install.py` instead.
* A browser that already has a file of that name saves the new one as `pitrelay-claude-code (1).zip`. Use that name, in quotes.

To check the download first, compare its SHA-256 with the one on the download page: `shasum -a 256 <file>` (macOS), `sha256sum <file>` (Linux), `Get-FileHash <file>` (PowerShell).

Download and run in one line (works while the repository is public):

* macOS, Linux: `curl -fLO https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip && python3 pitrelay-claude-code.zip`
* Windows PowerShell: `curl.exe -fLO https://github.com/catswert/pitrelay-info/raw/claude-code/pitrelay-claude-code.zip; py pitrelay-claude-code.zip`

If the repository is private, download the ZIP in a browser where you are signed in to GitHub, then run step 2.

## What the installer does

1. **Unpacks itself** into the installer's folder (below; once per download), then checks every file against `SHA256SUMS.txt`.
2. **Finds a Python 3.10+.** Started by an older one (macOS's own `python3` is 3.9), it looks for a newer Python on the computer and continues with that. It looks at python.org, Homebrew and pyenv installs, the Windows `py` launcher and Python install manager, and the Pythons Windows has registered.
3. **Finds Java 17+**, in this order:
   * `JAVA_HOME`, then `java` on your PATH;
   * on macOS, `/usr/libexec/java_home`;
   * WPILib's JDK, newest season first: `C:\Users\Public\wpilib\<year>` on Windows, `~/wpilib/<year>` on macOS and Linux, and from 2027 `~/.wpilib/<year>` (macOS) or `~/.local/share/wpilib/<year>` (Linux);
   * the usual install folders of Temurin, Oracle, Microsoft, Zulu, Corretto and others, and Homebrew.

   Or name one with `--java <path>`.
4. **Shows the PitRelay license.** Type `accept`.
5. **Prepares the plugin** with `plugin/configure_claude.py` (about a minute). This:
   * checks the package, Java and Python;
   * installs the collector's two Python packages (`paramiko`, `keyring`) from PyPI into a private environment;
   * writes the plugin into a new folder.
6. **Prints a `receipt_sha256`.** Copy it into your notes: `verify` uses it to detect changes later.
7. **Asks before adding PitRelay to Claude Code**, because that changes your Claude Code settings.
   * First, `claude plugin validate` checks that Claude Code accepts the new plugin.
   * Then it runs `claude plugin marketplace add <folder>` and `claude plugin install pitrelay@pitrelay-local`.
   * If something fails after an earlier installation was removed, it prints the commands that put the earlier one back.
   * **Without the `claude` command** (for example with only the desktop app), it offers to add PitRelay to Claude Code's settings file instead (`~/.claude/settings.json`; Windows: `%USERPROFILE%\.claude\settings.json`). It adds the same two entries the `claude` commands write there, and keeps a copy of the previous file. Claude Code loads PitRelay from there when a new session starts.
8. **Prints the commands for later**: check and uninstall. They run the installer's unpacked copy, so you do not need the ZIP again.

| | macOS | Linux | Windows |
|---|---|---|---|
| Installer folder: the prepared plugin, PitRelay's state, the unpacked download | `~/Library/Application Support/PitRelay for Claude Code/` | `~/.local/share/pitrelay-claude/` | `%LOCALAPPDATA%\PitRelayClaude\` |
| Collected logs (shared with the standalone collector) | `~/Library/Application Support/PitRelayCollector/` | `~/.local/share/pitrelay-collector/` | `%LOCALAPPDATA%\PitRelayCollector\` |

Do not move or delete the prepared plugin folder (`marketplace-<date>-<time>`) while PitRelay is installed: Claude Code loads it from there. Keep the unpacked copy in `downloads/`, too: the check and uninstall commands use it. The downloaded ZIP is not needed afterwards.

Pressing Ctrl-C while the plugin is being prepared is safe. The installer waits for setup to remove what it had created.

## Using it

1. Start a **new** Claude Code session (`claude` in a terminal, in any folder).
2. `/pitrelay:start` reports the engine, the access level and the collector. `/mcp` should list `plugin:pitrelay:pr` as connected.
3. `/pitrelay:collect` sets up the sources. You confirm each source in a dialog. Then a second dialog shows the robot's SSH host key to compare and trust. Typical sources:
   * the roboRIO: user `lvuser`, blank password, folders `/home/lvuser/logs` and `/u/logs`, address `172.22.11.2` (USB) or `10.TE.AM.2`;
   * the Driver Station laptop's log folder, for example `C:\Users\Public\Documents\FRC\Log Files`.
4. Then ask in plain words, for example "collect the logs and look at the brownout in the last match", or use `/pitrelay:diagnose <symptom>`.

Claude Code asks before each tool call. To allow a tool you trust permanently, use `/permissions`, for example `mcp__plugin_pitrelay_pr__collector_status`.

A robot password, if your team set one, is never typed into the chat. The collector opens its own page in your browser for it and keeps it in the system password store.

### In the Claude desktop app

* The desktop app's local sessions read the same settings as the command line, so PitRelay appears there once it is installed. Not tested.
* Adding a source normally asks you to confirm in a dialog. The desktop app may not show these dialogs, and then the change is cancelled. If that happens, ask Claude to **open the collector page**, and add the robot and the Driver Station folder there, in your browser.
* Plugins do not load in the desktop app's WSL sessions. On Windows, install PitRelay on Windows itself, not inside WSL.

## Requirements

1. **Claude Code**, the command line or the desktop app (tested with the command line, 2.1.310). To install the command line ([code.claude.com/docs/en/setup](https://code.claude.com/docs/en/setup)):
   * macOS, Linux: `curl -fsSL https://claude.ai/install.sh | bash` (macOS also: `brew install --cask claude-code`)
   * Windows PowerShell: `irm https://claude.ai/install.ps1 | iex` (or: `winget install Anthropic.ClaudeCode`)

   With only the desktop app, the installer adds PitRelay to Claude Code's settings file instead ([above](#what-the-installer-does)).
2. **Java 17 or later.** Check with `java -version`. If you have none:
   * Windows: `winget install -e --id EclipseAdoptium.Temurin.21.JRE`, or the installer from [adoptium.net](https://adoptium.net)
   * macOS: the `.pkg` from [adoptium.net](https://adoptium.net), or `brew install --cask temurin@21`
   * Ubuntu 22.04 or later, Debian 13: `sudo apt install openjdk-21-jre-headless`; Debian 12: `sudo apt install openjdk-17-jre-headless`; Fedora: `sudo dnf install java-21-openjdk-headless`
3. **Python 3.10 or later** (CPython). Check with `python3 --version` (Windows: `py --version`).
   * Windows: [python.org](https://www.python.org/downloads/), or `winget install -e --id Python.Python.3.13 --scope user`. Either gives you the `py` command. The Microsoft Store Python does not work: its path changes with every update.
   * macOS: [python.org](https://www.python.org/downloads/macos/) or `brew install python@3.12`. Apple's own `python3` is too old, but the installer moves on to a newer one by itself.
   * Ubuntu/Debian: `sudo apt install python3 python3-venv` (the collector needs `venv`); Fedora: `sudo dnf install python3`.

   Keep this Python installed: Claude Code starts PitRelay with it every time.
4. **Internet access** during the install, for the collector's Python packages from PyPI. Without internet, see [Offline computers](#offline-computers).

About 60 MB of disk, plus the collected logs over time.

## Options

Add them after the file name, for example `python3 pitrelay-claude-code.zip --java /path/to/java`.

| Option | Effect |
|---|---|
| `--java <path>` | The Java to use |
| `--python <path>` | The Python PitRelay runs with (default: the one running the installer) |
| `--folder <dir>` | Another installer folder. Upgrades find it again by themselves, through the installation Claude Code has. |
| `--collector-store <dir>` | Another folder for collected logs |
| `--without-collector` | The engine only: no collector, no Python packages. `--with-collector` brings the collector back in an upgrade. |
| `--wheelhouse <dir>` | Install the collector's Python packages from a folder of wheels (offline) |
| `--workspace <dir>` | Workspace Access: PitRelay may also read this folder (the robot project, for example). The installer shows PitRelay's disclosure first. The grants default to `project_read,propose`; change them with `--grants`. `--standard-access` drops it in an upgrade. |
| `--accept-license <sha256>` | Accept the license without the prompt (for scripts) |
| `--no-claude` | Prepare only, and print the commands that add it to Claude Code |
| `--yes` | Answer yes to the installer's own questions (not the license) |
| `--claude <path>` | The `claude` command, if it is not on your PATH |
| `--version` | Print the version |

`doctor` instead of an option (`python3 pitrelay-claude-code.zip doctor`) lists the Pythons, Javas and Claude Code the installer finds, and what is installed. Include it when you ask for help.

## Check, upgrade, uninstall

Use the commands the installer printed at the end. Or run the ZIP again with `verify` or `uninstall` after the file name.

* **Check:** `verify --receipt-sha256 <value from your notes>`. It checks every file of the prepared plugin, the launch configuration, the Java and Python it starts, and the collector's Python environment.
  * Without `--receipt-sha256`, it compares the receipt with the copy the installer recorded on this computer. That detects accidental changes only, and the Java and Python are not run.
  * PitRelay also checks its own files each time it starts, and `/pitrelay:start` shows the result.
* **Upgrade:** run a newer ZIP. Close every Claude Code session that uses PitRelay first.
  * It keeps PitRelay's state (cases, artifacts) and the collected logs.
  * It uses the options of your earlier installation again: its folder, Workspace Access, and with or without the collector.
  * It prepares the new version in a new folder, and replaces the old one in Claude Code: `claude plugin marketplace remove pitrelay-local`, then `add` and `install`.
  * If background collection was on, turn it on again in the new version first (ask Claude: "turn on background collection"). Then delete the old prepared folder the installer names.
* **Uninstall:** `uninstall`.
  * It turns off launch at login if background collection was on (after asking).
  * It runs `claude plugin uninstall pitrelay@pitrelay-local` and `claude plugin marketplace remove pitrelay-local`.
  * It lists what is left, for you to delete yourself: Claude Code's cached copy of the plugin (`~/.claude/plugins/cache/pitrelay-local/`; Windows: `%USERPROFILE%\.claude\plugins\cache\pitrelay-local\`), the prepared plugin folders, the unpacked downloads, PitRelay's state and the collected logs. Nothing deletes logs for you.
  * It exits with an error when something was not removed.

## Install by hand

The installer only automates these steps. Unzip the file (Linux: `unzip pitrelay-claude-code.zip -d pitrelay-claude-code`; Windows: right-click, *Extract All*). In that folder, `python3 install.py` (Windows: double-click `install.cmd`) is the same installer as running the ZIP. To do it all yourself instead:

1. Read `pitrelay-0.1.0-dev-20261024-engine-fixes-1/docs/release/PITRELAY_LICENSE.md`. Its SHA-256 is the license hash in the table above.
2. Prepare the plugin into two **new** folders (one line). Every path must be absolute:

   ```text
   python3 plugin/configure_claude.py --package <absolute path>/pitrelay-0.1.0-dev-20261024-engine-fixes-1
       --manifest-sha256 e62f01a685dbd1783a1af9211bc49303fdf3fa093ed202e2faf89df2788b5723
       --accept-license 04bccdf9562964c3f6b9bd030f84a5d504a7c69910355400bfbfd4e3ec3878a4
       --java <absolute path of java> --state <new state folder> --output <new plugin folder>
   ```

   All its options are in `plugin/README.md` (section *Set up*).
3. Add it to Claude Code: `claude plugin marketplace add <new plugin folder>`, then `claude plugin install pitrelay@pitrelay-local`. Or, inside a Claude Code session: `/plugin marketplace add <new plugin folder>`, then `/plugin install pitrelay@pitrelay-local`.

## Offline computers

On a computer with internet and the same operating system and Python version, unzip the file and run:

```sh
python3 plugin/configure_claude.py wheelhouse --dest <new folder>
```

For another platform, add for example `--platform win_amd64 --python-version 3.12`. Copy that folder to the offline computer and run the installer with `--wheelhouse <path of that folder>`.

## Troubleshooting

| Problem | What to do |
|---|---|
| `Java 17 or later was not found` | Install Java ([Requirements](#requirements)) and open a new terminal, or pass `--java <path>` |
| `no suitable Python was found` | Install Python ([Requirements](#requirements)) and run the ZIP with it: `python3.13 …` on macOS, `py …` on Windows |
| Anything else | Run `doctor` ([Options](#options)): it shows what the installer finds |
| A path "passes through a link" | Your home folder or a chosen folder is a symbolic link. Pass the real paths with `--folder` and `--collector-store`. |
| "There is no terminal to answer in" | Git Bash on Windows: run the installer from PowerShell or Command Prompt instead. Elsewhere: run it in a terminal, or pass `--accept-license <sha256>` and `--yes`. |
| "probably an earlier attempt that was cut off" | Delete the folder it names and run the installer again |
| `install the python3-venv package` | Ubuntu/Debian: `sudo apt install python3-venv`, then run the installer again |
| pip cannot reach PyPI | Use a wheelhouse ([Offline computers](#offline-computers)) or `--without-collector` |
| `claude` not found | Let the installer add PitRelay to the settings file, or install the Claude Code command line ([Requirements](#requirements)) and run the installer again |
| Typing `claude` opens the desktop app (Windows) | An older Claude desktop app took over the name; update the desktop app. The installer skips that alias. |
| `/mcp` shows that the server failed | Run `verify`. If Java or Python was removed, or upgraded to another major version, run the installer again. |

## What the fixed engine changes

Details are in `docs/ENGINE-FIXES-1.md`.

* **REVLOG 2.x** files (REVLib 2027.0.0-alpha-9 and later) are decoded: SPARK status fields, robot state, match info and logger events. The v11 engine decoded nothing in them.
* **Driver Station files.** A `.dslog` read as `.dsevents` (or the reverse) gets the right format named instead of a damage report. All-zero records, from a log cut short by a power loss, are reported as a truncated tail instead of 0 V readings.
* **Uploads** return `transfer_id`, and uploading inside a workflow no longer uses up the workflow's call budget.
* **CSV time units.** A real unit the engine does not support is `unsupported`, and a typo is an `error`.

The plugin's three "host notes" for v11 problems switch off by themselves with this engine, because the problems are fixed in it.

## What was tested, and what was not

Tested on Linux (Claude Code 2.1.310, Java 21):

* **Installs into empty Claude Code configurations.** This ZIP, run directly and unpacked, with Python 3.10, 3.11, 3.12 and 3.13: interactively and with `--yes`, with and without the collector. Then:
  * `verify`;
  * `claude mcp list`, where the server connects;
  * the plugin's server driven exactly as Claude Code starts it: 121 tools, collection from a folder, export, and analysis by the fixed engine of a WPILib log, both Driver Station files and a REVLOG 2.0 file (17 of 17 checks).
* **Upgrade** (the state and the collected logs are kept) and **uninstall** (including turning off launch at login).
* **Without the `claude` command:** PitRelay added to the settings file (other settings kept, a backup made), a new session connecting to it, upgrade and uninstall the same way.
* **The refusals:** a changed or added file, a wrong license hash, a state folder the installer did not make, no Java. Finding Java and Python (WPILib folders, Apple's `python3`, the Windows install manager) was tested with stand-in programs on Linux.
* **The engine:**
  * PitRelay's own test suite, 2614 tests, with the same 3 failures as the unchanged v11 source on that computer, which lacks WPILib's native libraries;
  * 19 new tests;
  * an independent second REVLOG 2.x decoder, which agreed on 318,728 values.
* **The plugin:** 352 tests, the collector's 229, and mutation checks.

**Not tested:** Windows and macOS (the commands and paths for them come from their vendors' documentation), the Claude desktop app and IDE extensions, a live Claude session choosing the tools, a real robot or Driver Station, and real REVLib 2.x or Driver Station logs (synthetic logs only). The engine was compiled with Java 21 for Java 17, using the WPILib classes from the v11 JAR. Rebuild it with PitRelay's reference setup before calling it a release.

## Safety boundaries

* The collector only **reads** from robots and Driver Stations. It never deploys code, moves mechanisms, sends control values, or deletes or changes files there.
* It contacts only sources you confirmed, and stops on an unknown or changed SSH host key before sending any password.
* Passwords stay in the system password store, never in the chat, exports or logs.
* **Nothing is uploaded.** Collected logs and exported packages stay on your computer and are not anonymized.
* Background collection (collecting while Claude Code is closed) stays off until you turn it on in a dialog.
* Collected logs are never deleted automatically.
