<h2><img align="middle" src="https://raw.githubusercontent.com/odb/official-bash-logo/master/assets/Logos/Icons/PNG/64x64.png" >
Octodinator - Coordinated Init & Cleanup for Multiple Programs.
</h2>

Written by Llewellyn van der Merwe (@llewellynvdm)

A simple CLI utility to **coordinate one-time initialization and cleanup** across multiple programs or scripts.
Runs a specified *init* command when the **first program** starts, and a *cleanup* command when the **last program** stops.
Uses a shared lock directory to track active programs and prevent duplicate init/cleanup runs.
Supports `.json` configuration files and CLI overrides for full flexibility.

Linted by [#ShellCheck](https://github.com/koalaman/shellcheck)

---

## Install

```shell
$ sudo curl -L "https://git.vdm.dev/api/v1/repos/octoleo/octodinator/raw/main/octodinator" -o /usr/local/bin/octodinator
$ sudo chmod +x /usr/local/bin/octodinator
```

* Global **config** file can be set at: `/home/$USER/.config/octodinator/config.json`
* OR use the `--conf=/path/to/config.json` flag to load a project-specific configuration.

**Configuration File (config.json):**

```json
{
  "lock_dir": "/tmp/octodinator",
  "init_command": "octoflare --domain=example.com --disable-attack-mode",
  "cleanup_command": "octoflare --domain=example.com --enable-attack-mode"
}
```

* `lock_dir`: Required. Directory used to store `.lock` files for running programs.
* `init_command`: Required. Command to run when the first program starts.
* `cleanup_command`: Required. Command to run when the last program stops.

---

## Usage

> To see the help menu

```shell
$ octodinator -h
```

---

> To update

```shell
$ octodinator --update
```

---

> To uninstall

```shell
$ octodinator --uninstall
```

---

### Help Menu (Octodinator)

```txt
Usage: octodinator [options] start|stop <id>

        Options
        ======================================================
        -ld | --lock-dir=<path>   Directory where lock files are stored
        -i  | --init=<command>    Command to run on first program start
        -c  | --cleanup=<command> Command to run on last program stop
        --conf=<path>             JSON config file with {lock_dir, init_command, cleanup_command}
        -q  | --quiet             Suppress all output
        --update                  Update this script to latest version
        --uninstall               Uninstall this script
        -h  | --help              Show this help menu
        ======================================================
                        Octodinator v1.0.0
        ======================================================
```

---

### Example

Start a program and trigger the init command if this is the first active one:

```shell
$ octodinator --lock-dir="/tmp/octodinator" \
              --init="octoflare --domain=example.com --disable-attack-mode" \
              --cleanup="octoflare --domain=example.com --enable-attack-mode" \
              start program1
```

Stop a program and trigger cleanup only if this was the last active one:

```shell
$ octodinator --lock-dir="/tmp/octodinator" stop program1
```

Using a config file:

```shell
$ octodinator --conf=/home/$USER/.config/octodinator/config.json start program2
$ octodinator --conf=/home/$USER/.config/octodinator/config.json stop program2
```

---

### Example Integration

In a script (`programA.sh`):

```bash
#!/bin/bash
octodinator start programA

# Program logic here
sleep 5

octodinator stop programA
```

When running multiple scripts with the same `--lock-dir`,
init runs **once at the first start**, cleanup runs **once at the very end**.

---

### Free Software License

```txt
@copyright  Copyright (C) 2025 Llewellyn van der Merwe. All rights reserved.
@license    GNU General Public License version 2; see LICENSE.txt
```
