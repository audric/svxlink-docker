# SvxLink Docker

Run [SvxLink](https://github.com/sm0svx/svxlink) in Docker — **svxlink**, **remotetrx**, or **svxreflector** — on `amd64` or `arm64` (Raspberry Pi included).

The standard svxreflector is replaced by [GeuReflector](https://github.com/audric/GeuReflector), an extended version that adds server-to-server trunk protocol for linking multiple reflectors together.

## Quick Start

```sh
git clone https://github.com/f4hlv/svxlink-docker.git
cd svxlink-docker
docker compose up -d
```

That's it. The pre-built image is pulled automatically.

## Configure

Extract the default config files from the container:

```sh
mkdir -p config/etc config/usr/share
docker compose cp svxlink:/etc/svxlink config/etc/
docker compose cp svxlink:/usr/share/svxlink config/usr/share/
```

Edit the files in `config/` to match your setup, then restart:

```sh
docker compose restart
```

## Choose which services to run

Set these in the `environment:` section of `docker-compose.yml`:

| Variable | Default | Description |
|---|---|---|
| `START_SVXLINK` | `1` | Enable svxlink |
| `START_REMOTETRX` | `0` | Enable remotetrx |
| `START_SVXREFLECTOR` | `0` | Enable svxreflector (GeuReflector) |

Extra options:

| Variable | Default | Description |
|---|---|---|
| `SVXLINK_ARGS` | *(empty)* | Extra CLI args (e.g. `--logfile=/var/log/svxlink/svxlink.log`) |
| `REMOTETRX_ARGS` | *(empty)* | Extra CLI args |
| `SVXREFLECTOR_ARGS` | *(empty)* | Extra CLI args |

## GeuReflector ports

When running the reflector (`START_SVXREFLECTOR=1`), these ports are available:

| Port | Description |
|---|---|
| `5300` | Client connections (same as standard svxreflector) |
| `5302` | Server-to-server trunk links |
| `5303` | Satellite relay connections (optional, uncomment in `docker-compose.yml`) |
| `8080` | HTTP status endpoints `/status` and `/config` (optional, uncomment in `docker-compose.yml`) |

Trunk and satellite ports are configured in `svxreflector.conf` via `[TRUNK_*]` and `[SATELLITE]` sections. See the [GeuReflector documentation](https://github.com/audric/GeuReflector) for details.

## Hardware access (sound card, GPIO, USB)

Uncomment the relevant lines in `docker-compose.yml`:

```yaml
devices:
  - /dev/snd:/dev/snd           # sound card
  - /dev/gpiochip0:/dev/gpiochip0   # GPIO (Raspberry Pi)
  - /dev/bus/usb:/dev/bus/usb   # USB / RTL-SDR
```

Or set `privileged: true` for full device access.

## Logs

```sh
docker compose logs -f --tail=500
```

## USRP Logic variant

To use the [dl1hrc/svxlink](https://github.com/dl1hrc/svxlink) USRP fork, change the image in `docker-compose.yml`:

```yaml
image: ghcr.io/audric/svxlink-docker-usrp:latest
```

## Building locally

To build from source instead of using the pre-built image, comment out `image:` and uncomment the `build:` block in `docker-compose.yml`, then:

```sh
docker compose up -d --build
```

Build from a different SvxLink or GeuReflector branch:

```sh
docker compose build --build-arg GIT_BRANCH=develop
docker compose build --build-arg GEUREFLECTOR_BRANCH=develop
docker compose up -d
```

Rebuild from scratch:

```sh
docker compose build --no-cache
docker compose up -d
```

## License

SvxLink is licensed under the **GPL**. This Docker setup follows the same spirit of openness and reuse.

---

Maintained by **F4HLV** — contributions welcome.
