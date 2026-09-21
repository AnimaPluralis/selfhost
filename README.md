# Etho Insights self-host releases

Public release feed for [Etho Insights](https://ethoinsights.com) Self-hosted Instances.
Each GitHub Release here carries `selfhost.tar.gz` (the installer bundle, with its own
quickstart) and `release.json` (the bundle checksum and the exact image digests it installs).

## Access

The app image on `ghcr.io/ethoinsights` is **private**. Installing needs two things from a
maintainer: an invitation to the `self-hosters` team, and a classic GitHub token with the
single scope `read:packages`, which the installer checks with one `docker login` before it
downloads anything. The invitation grants access to that image only, not to the source.
The CUDA model-server image is public and needs no token.

## Install

```bash
mkdir etho-download && cd etho-download
curl --fail --location --output selfhost.tar.gz \
  https://github.com/EthoInsights/selfhost/releases/latest/download/selfhost.tar.gz
tar -xzf selfhost.tar.gz
./install.sh
```

The application source repository is private; this repository holds only releases.
