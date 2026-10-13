# Run Anima Pluralis on your own machine

## For you: install in about ten minutes

You need a computer with Docker and the registry username and token the maintainer sent
you. You do not need a GitHub account. No source code, build tools or database are involved.

### 1. Keep your registry token at hand

The token lets this machine download the app from `registry.animapluralis.org`. Keep it in a
password manager; the installer asks for it once. It does not expire.

### 2. Check the machine

- **Docker** with Docker Compose 2.24 or newer (`docker compose version`).
- **Python 3.9 or newer.** Most Linux systems have it, and so does macOS once the Command Line
  Tools are installed. If Python is missing, the installer names the command to install it.
- **Linux on amd64.** arm64 Linux and Docker Desktop on a Mac are not tested yet.
- **To run models on this machine:** Linux with an NVIDIA GPU and the NVIDIA Container
  Toolkit. Without one you connect compute elsewhere (step 5).

### 3. Install

Run this on the machine that will host Anima Pluralis:

```bash
curl -fsSL https://github.com/AnimaPluralis/selfhost/releases/latest/download/install.sh | sh
```

The installer asks six questions:

| Question                    | What to answer                                                                                                                                                                     |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| What runs on this machine?  | `1` Anima Pluralis. `2` Anima Pluralis with models on this GPU (only offered when an NVIDIA GPU is found). `3` Models only, for an Anima Pluralis on another machine (see step 5). |
| Registry username and token | The username and token the maintainer sent you (step 1).                                                                                                                           |
| Organization or lab name    | The name your colleagues see. Press Enter for `My Organization`.                                                                                                                   |
| Your name                   | Shown on your account.                                                                                                                                                             |
| Administrator email         | The email you log in with.                                                                                                                                                         |
| How will you open Anima?    | `1` On this computer. `2` From your own computer, over SSH to this server. `3` From other computers on a private network such as Tailscale. `4` Through your own HTTPS domain.     |

It then installs into `~/.anima`, starts everything, and prints the login URL and a
**one-time administrator password**. Save the password now; it is never shown again.

### 4. Log in

Open the printed URL. If you chose `2` (over SSH), first run the `ssh` command the installer
printed on your own computer and leave it running. It looks like this:

```bash
ssh -N -L 127.0.0.1:3000:127.0.0.1:3000 you@your-server
```

Then open <http://localhost:3000>.

### 5. Connect compute

A fresh install cannot run models yet. Pick one option in **Settings → Compute**:

- **Your own Modal account.** Works from any machine, and you pay per use. Connect a Modal
  workspace with a Service User token. Service Users need a Modal Team or Enterprise plan;
  Modal bills the plan and compute to that workspace. Your footage passes through that Modal account.
- **This machine's GPU** (Linux with an NVIDIA GPU). If you answered `2` in step 3, this is
  already set up. To add it later, run `anima compute enable`. Script Nodes and Assistant
  Analysis do not run on this option.
- **A GPU on another machine you run.** Run the same install command there and answer `3`.
  It skips the other questions. Then run `anima compute pair` on that machine, and paste the
  code it prints into **Compute on another machine** in Settings.

### 6. Check that it works

1. In **Library**, upload a short video, wait for processing, and play it.
2. In **Analyzers**, choose **New Analyzer → Species ID**.
3. Back in **Library**, select the video and choose **Analyze → Species ID**.

The first run downloads model weights, so it can take a while. A completed Result with no
species found is fine for non-animal footage. If the Result failed, run `anima logs` to see
why.

### Everyday commands

```bash
anima status     # what is running, installed version, unfinished update
anima up         # start again after a reboot or anima stop
anima stop       # stop everything (let running jobs finish first)
anima update     # install the newest version; asks before changing anything
anima doctor     # check Docker, ports, GPU and health (it changes nothing)
anima logs       # recent logs; add --follow to keep watching
```

The installer offers to add `anima` to your `PATH`; open a new terminal afterwards. If your
shell still says `anima: command not found`, use `~/.anima/anima` instead.

Everything lives in `~/.anima`. To back it up, follow the cold-copy backup in
`~/.anima/recovery.md`: stop Anima, copy the whole folder, then start it again. Copying only
part of it, or copying while it runs, does not give you a backup you can restore.

If an update stops with "The registry rejected your registry token", your token was revoked
or mistyped. Ask info@animapluralis.org for a new one, then run
`docker login registry.animapluralis.org --username <your-registry-username>` and paste it at
the password prompt.

### More help

- The full guide is `~/.anima/README.md`. Next to it, `configuration.md` covers
  networking, storage, email, HTTPS links and separate model servers, and `recovery.md`
  covers backups, lost passwords and interrupted updates.
- To share the instance with colleagues, see `configuration.md` (a private network, or
  `anima up --secure` for an HTTPS link).
- After the check above, open **Wiki & guides** (the book icon in the sidebar) for how to
  work in the app.
- If you have a problem, write to info@animapluralis.org with the output of
  `anima status --json` and `anima doctor --json`. Neither one prints secrets.
