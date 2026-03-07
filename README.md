# SvxLink Docker

Dockerized [SvxLink](https://github.com/sm0svx/svxlink) — run **svxlink**, **remotetrx**, and/or **svxreflector** from a single multi-arch image (`amd64` / `arm64`).

Pre-built images are published to GitHub Container Registry on every push.

## Quick Start

```sh
git clone https://github.com/f4hlv/svxlink-docker.git
cd svxlink-docker
docker compose up -d
```

This builds the image locally and starts svxlink with the default configuration.

### Extract default config files (first run)

```sh
mkdir -p config/etc config/usr/share
docker compose cp svxlink:/etc/svxlink config/etc/
docker compose cp svxlink:/usr/share/svxlink config/usr/share/
```

Edit files in `config/` to match your setup, then restart:

```sh
docker compose restart
```

### Use a pre-built image (skip the build)

Replace the `build:` block in `docker-compose.yml` with:

```yaml
image: ghcr.io/audric/svxlink-docker:master
```

For the USRP Logic variant (dl1hrc fork):

```yaml
image: ghcr.io/audric/svxlink-docker-usrp:master
```

## Environment Variables

Control which services run and how, in `docker-compose.yml`:

| Variable | Default | Description |
|---|---|---|
| `START_SVXLINK` | `1` | Start svxlink |
| `START_REMOTETRX` | `0` | Start remotetrx |
| `START_SVXREFLECTOR` | `0` | Start svxreflector |
| `SVXLINK_CONF` | `/etc/svxlink/svxlink.conf` | Config file path |
| `REMOTETRX_CONF` | `/etc/svxlink/remotetrx.conf` | Config file path |
| `SVXREFLECTOR_CONF` | `/etc/svxlink/svxreflector.conf` | Config file path |
| `SVXLINK_ARGS` | *(empty)* | Extra CLI args (e.g. `--logfile=/var/log/svxlink/svxlink.log`) |
| `REMOTETRX_ARGS` | *(empty)* | Extra CLI args |
| `SVXREFLECTOR_ARGS` | *(empty)* | Extra CLI args |

## Hardware Access (Raspberry Pi / Sound Cards / GPIO)

Uncomment the relevant lines in `docker-compose.yml`:

```yaml
devices:
  - /dev/snd:/dev/snd
  - /dev/gpiochip0:/dev/gpiochip0
  # - /dev/bus/usb:/dev/bus/usb   # for RTL-SDR
```

Or enable `privileged: true` for full device access.

## Building Locally

To build from a different branch or fork:

```sh
docker compose build --build-arg GIT_BRANCH=develop
docker compose up -d
```

To use the USRP Logic variant, set `dockerfile: Dockerfile.usrp` in `docker-compose.yml`.

Rebuild from scratch:

```sh
docker compose build --no-cache
docker compose up -d
```

## Logs

```sh
docker compose logs -f --tail=500
```

## License

SvxLink is licensed under the **GPL**.
This Docker setup follows the same spirit of openness and reuse.

---

Maintained by **F4HLV** — contributions welcome.
