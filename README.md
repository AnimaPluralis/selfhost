# Etho Insights self-host releases

Public release feed for [Etho Insights](https://ethoinsights.com) Self-hosted Instances.
Each GitHub Release here carries `selfhost.tar.gz` (the installer bundle, with its own
quickstart) and `release.json` (the bundle checksum and the exact public image digests it
installs). The container images are public on `ghcr.io/ethoinsights`.

```bash
mkdir etho-download && cd etho-download
curl --fail --location --output selfhost.tar.gz \
  https://github.com/EthoInsights/selfhost/releases/latest/download/selfhost.tar.gz
tar -xzf selfhost.tar.gz
./install.sh
```

The application source repository is private; this repository holds only releases.
