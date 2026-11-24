<div align="center">

# kpi

**The official package manager for KOOMPI OS & KRAMA OS**

One place for every package: official repositories, the AUR, and Cambodian software.

[![License: GPL v3](https://img.shields.io/badge/license-GPLv3-blue.svg)](LICENSE)
![Go](https://img.shields.io/badge/built%20with-Go-00ADD8?logo=go&logoColor=white)
![Platform](https://img.shields.io/badge/platform-Arch%20based-1793D1?logo=archlinux&logoColor=white)

![kpi demo](https://i.imgur.com/D9w8YF0.png)

</div>

---

## Table of contents

- [Why kpi](#why-kpi)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Building from source](#building-from-source)
- [Project layout](#project-layout)
- [Contributing](#contributing)
- [Credits](#credits)
- [License](#license)

## Why kpi

KOOMPI and KRAMA OS are built on Arch Linux. `kpi` wraps `pacman` and the AUR behind a single, friendly command so users never have to think about where a package comes from. It aims to:

- Provide a simple interface on top of `pacman`
- Offer Yaourt-style interactive search and install
- Keep dependencies minimal and user prompts few
- Know when `-git` packages are due for an upgrade
- Bring Cambodian packages together in one place

## Features

- **Advanced dependency solving** across repositories and the AUR
- **PKGBUILD download** from ABS or the AUR
- **Tab completion** for bash, zsh and fish, including AUR package names
- **Ask everything up front**: all questions are asked before any build starts
- **Narrowing search**: `kpi linux header` searches `linux`, then narrows to `header`
- **Provider selection** when several packages provide the same target
- **Clean-up** of make dependencies after a build
- **Safe parsing**: reads `.SRCINFO` and never sources a PKGBUILD

## Installation

### Requirements

| Dependency   | Needed for          |
| ------------ | ------------------- |
| `git`        | runtime             |
| `base-devel` | runtime (building)  |
| `go`         | building only       |

### Install

```sh
sudo pacman -S --needed git base-devel go
git clone https://github.com/lihourchhin/kpi.git
cd kpi
make
sudo make install
```

`make install` places the binary, man page and shell completions under `/usr/local`. Set `PREFIX` to change that:

```sh
sudo make install PREFIX=/usr
```

### Uninstall

```sh
sudo make uninstall
```

## Usage

### Everyday commands

| Command                | What it does                                    |
| ---------------------- | ----------------------------------------------- |
| `kpi <term>`           | Search and pick packages to install interactively |
| `kpi -i <package>`     | Install a package                               |
| `kpi -S <package>`     | Install a package (pacman style)                |
| `kpi -R <package>`     | Remove a package                                |
| `kpi`                  | Upgrade the whole system (repos + AUR)          |
| `kpi -Ss <term>`       | Search repositories and the AUR                 |
| `kpi -Qu`              | List available upgrades                         |

### kpi-specific operations

| Command                | What it does                                    |
| ---------------------- | ----------------------------------------------- |
| `kpi -G <package>`     | Download a package's PKGBUILD                   |
| `kpi -Ps`              | Show system package statistics                  |
| `kpi -Pw`              | Print Arch Linux news                           |
| `kpi -Yc`              | Remove unneeded dependencies                    |
| `kpi -Y --gendb`       | Build the development-package database for `-git` upgrades |
| `kpi -a <term>`        | Only target the AUR                             |
| `kpi --repo <term>`    | Only target the official repositories           |

Every standard `pacman` operation (`-D`, `-F`, `-Q`, `-T`, `-U`, …) is passed through unchanged.

Full reference:

```sh
kpi --help
man kpi
```

## Configuration

Add `--save` to any permanent option to store it in the config file:

```sh
kpi --editor nvim --sudoloop --save
```

| Command        | Shows                       |
| -------------- | --------------------------- |
| `kpi -Pg`      | Current configuration       |
| `kpi -Pd`      | Default configuration       |

Settings are stored as JSON in `$XDG_CONFIG_HOME/yay/config.json` (default `~/.config/yay/config.json`), and the cache lives in `~/.cache/yay`. These paths are inherited from yay and are shared with it.

## Building from source

```sh
make          # build ./kpi
make test     # gofmt check, go vet and unit tests
make package  # build a release tarball: kpi_<version>_<arch>.tar.gz
make clean    # remove build output
```

Override the version at build time with `make VERSION=0.1.0`.

Third-party Go dependencies are vendored in `vendor/` and are built with `-mod=vendor`, so no network access is needed during the build. Do not edit files in `vendor/` by hand.

| Vendored dependency                                                 | Purpose                    |
| ------------------------------------------------------------------- | -------------------------- |
| [jguer/go-alpm](https://github.com/Jguer/go-alpm)                   | libalpm (pacman) bindings  |
| [Morganamilo/go-srcinfo](https://github.com/Morganamilo/go-srcinfo) | `.SRCINFO` parser          |
| [Morganamilo/go-pacmanconf](https://github.com/Morganamilo/go-pacmanconf) | `pacman.conf` parser |
| [mikkeloscar/aur](https://github.com/mikkeloscar/aur)               | AUR RPC client             |

## Project layout

```
.
├── main.go, cmd.go, parser.go   # entry point, CLI parsing and dispatch
├── install.go, upgrade.go       # install and system-upgrade flows
├── dep*.go                      # dependency resolution
├── download.go, vcs.go          # PKGBUILD download and -git tracking
├── query.go, print.go           # search and output
├── completions/                 # bash, zsh and fish completions
├── doc/                         # man page
├── testdata/                    # fixtures for unit tests
└── vendor/                      # vendored Go dependencies
```

## Contributing

1. Fork the repository and create a branch: `git checkout -b feature/my-change`
2. Make your change and run `make test`
3. Commit with a clear message and open a pull request

To report a bug, please open an issue and include the output of `kpi -V` and the exact command you ran.

## Credits

kpi is a fork of [yay](https://github.com/Jguer/yay) and draws on the design of [yaourt](https://github.com/archlinuxfr/yaourt), [apacman](https://github.com/oshazard/apacman) and [pacaur](https://github.com/rmarquis/pacaur). Thank you to their authors and contributors.

## License

kpi is free software released under the [GNU General Public License v3.0](LICENSE).
