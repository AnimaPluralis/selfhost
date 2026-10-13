# Anima Pluralis self-host releases

Release feed for running Anima Pluralis on your own machine. Anima Pluralis is a platform for
animal-behavior research: bring recordings, find candidate evidence, and measure which methods
work on your own footage.

This repository holds only Releases and the install instructions. The application source is
private, and installing needs no source code, build tools or database.

## Install

On the machine that will host Anima Pluralis, with Docker, Docker Compose 2.24 or newer and
Python 3.9 or newer:

```bash
curl -fsSL https://github.com/AnimaPluralis/selfhost/releases/latest/download/install.sh | sh
```

New self-hosters: follow [Run Anima Pluralis on your own machine](install.md). It walks from
the registry token to a first Result: requirements, the installer's questions, logging in,
connecting compute and a first check.

After installing, `anima update` installs a newer Release. The full guide is installed as
`~/.anima/README.md`.

## Access

The app image lives on the registry at `registry.animapluralis.org` and needs a login.
The maintainer issues you a registry username and token; you do not need a GitHub account.
The installer asks for both once. The model-server and sandbox images are public, and so is
everything in this repository.

To request access, or if your token stops working, write to info@animapluralis.org.

## What each Release carries

Every Release has exactly three assets:

- `install.sh`: the installer. Run on its own, as in the command above, it downloads
  `release.json` and `selfhost.tar.gz` from the same Release, checks the archive's SHA256
  against `release.json`, and runs the bundled installer.
- `selfhost.tar.gz`: the installer bundle, with the `anima` command, the Compose files and the
  guides (`README.md`, `configuration.md`, `recovery.md`).
- `release.json`: the Release's identity: its version, the archive and installer checksums,
  and the exact image digests it installs.

To download and extract the bundle yourself instead of piping the installer to `sh`, use a
separate download directory:

```bash
mkdir anima-download && cd anima-download
curl --fail --location --output selfhost.tar.gz \
  https://github.com/AnimaPluralis/selfhost/releases/latest/download/selfhost.tar.gz
tar -xzf selfhost.tar.gz
./install.sh
```
