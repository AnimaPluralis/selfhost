# Run Etho Insights on your own machine

## Install in about ten minutes

You need a computer with Docker, a GitHub account, and the invitation from the maintainer.
No source code, build tools or database are involved.

### 1. Accept and create a token

1. Accept the GitHub organization invitation from your email. It only grants permission to
   download the app image.
2. Create a **classic** token with the single scope `read:packages`:
   <https://github.com/settings/tokens/new?scopes=read:packages&description=Etho%20Insights%20self-host>.
   Fine-grained tokens do not work for container images. Store the token in a password
   manager; the installer asks for it once.

### 2. Check the host

- Python 3.9 or newer (already present on macOS via the Command Line Tools and on most
  Linux); Docker with Linux containers and Docker Compose 2.24+ (`docker compose version`).
- `bash`, `curl` and `tar` on the same host.
- Linux amd64 or arm64, or Docker Desktop on a Mac. Apple Silicon runs the app but cannot run
  models locally; see step 5.
- Optional, Linux only: an NVIDIA GPU with the NVIDIA Container Toolkit if you want models to
  run on this machine.

### 3. Install

```bash
mkdir etho-download && cd etho-download
curl --fail --location --output selfhost.tar.gz \
  https://github.com/EthoInsights/selfhost/releases/latest/download/selfhost.tar.gz
tar -xzf selfhost.tar.gz
./install.sh
```

The installer asks for:

| Prompt                         | Answer                                                                                                                                       |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| GitHub handle and token        | Your handle and the `read:packages` token. It checks them with one `docker login` before downloading anything.                               |
| Browser URL                    | Press Enter for `http://localhost:3000` on the computer you sit at. On a remote server also press Enter, then use the SSH forward in step 4. |
| Administrator email            | The address you will log in with.                                                                                                            |
| Bundle the model on this host? | Asked only when an NVIDIA runtime is present. Yes installs the `cuda` profile. This choice cannot be changed later without reinstalling.     |

It installs into `~/.etho-insights`, starts the services and prints the login URL and a
**one-time administrator password**. Save the password now; it is never shown again.

### 4. Log in

Open the printed URL. On a headless server, first run this on your own computer and keep it
open:

```bash
ssh -N -L 127.0.0.1:3000:127.0.0.1:3000 you@your-server
```

then open `http://localhost:3000`. Email notifications stay off until you configure them in
Settings → Notifications; nothing else depends on them.

### 5. Connect compute

A fresh install runs no models. In Settings → Compute choose one:

- **Your own Modal account** (any machine, pay per use): connect a Modal workspace with a
  Service User token. Footage transits your Modal account.
- **This machine's GPU** (Linux, NVIDIA): already set up if you chose the `cuda` profile.
  Script Nodes and Assistant Analysis are unavailable on this target.
- **Another GPU machine you operate**: add it as "Etho model server at a URL" with its bearer
  secret. Setup is in the bundled `configuration.md`.

### 6. Check it works

In Library upload any short video, wait for processing, and play it. Then in Analyzers create
**New Analyzer → Species ID**, go back to Library, select the video and choose **Analyze →
Species ID**. The first run downloads model weights and can take a while. A completed Result
with zero species candidates on non-animal footage is fine; a failed Result is not, so check
`etho logs`.

### Day to day

```bash
etho status     # services, installed release, pending update
etho up         # after a reboot or etho stop
etho stop       # wait for running jobs first
etho update     # newest release; asks before changing anything
etho doctor     # Docker, port, GPU and health checks; repairs nothing
etho logs       # last 100 lines; --follow for a live tail
```

If `etho` is not found, run `~/.etho-insights/etho` instead, or add `~/.local/bin` to your
`PATH`. Everything lives under `~/.etho-insights`: `data/` (databases and Media), `secrets/`
(encryption keys, back these up together with `data/`), `model-cache/`, `backups/`.

If a pull later says `denied`, your token expired or your access was removed. Create a new
token and run `docker login ghcr.io --username <handle>`; if that still fails, ask the
maintainer.

### Next steps

- `~/.etho-insights/README.md` is the full guide, with `configuration.md` (network, storage,
  email, HTTPS link, separate model server) and `recovery.md` (lost password, interrupted
  update) beside it.
- Share the instance with colleagues over a private network or `etho up --secure`, see
  `configuration.md`.
- Design Analyzers: the in-app help and the Analyzer wiki that ships with the hosted product.
- Questions and problems: send `etho status --json` and `etho doctor --json` output (neither
  prints secrets) to the maintainer.

