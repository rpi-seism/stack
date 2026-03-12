# RPI-Seism Stack

One-command deployment for the full [rpi-seism](https://github.com/rpi-seism) seismic monitoring system.  
This repository wires together all components via Git submodules and a single `docker-compose.yml`.

---

## Repository structure

```
stack/
├── daemon/     # acquisition daemon (submodule)
├── reader/     # Arduino firmware   (submodule, reference only)
├── api/        # FastAPI archive    (submodule)
├── web/        # Angular frontend   (submodule)
├── docker-compose.yml      # full stack
├── docker-compose.dev.yml  # dev overrides (fake WebSocket, no hardware)
└── .env.example            # environment variable template
```

---

## Prerequisites

- Docker + Docker Compose
- A Raspberry Pi with a USB-RS485 adapter connected to the Arduino digitizer
- `UID` / `GID` of the user that should own the data files

---

## Quick start

```bash
# 1. Clone with all submodules
git clone --recurse-submodules https://github.com/rpi-seism/stack
cd stack

# 2. Configure
cp .env.example .env
nano .env   # set UID, GID, SERIAL_PORT, NETWORK, STATION

# 3. Deploy
docker compose up -d

# 4. Check everything started
docker compose ps
docker compose logs -f
```

The services will be available at:

| Service | URL |
|---|---|
| Live dashboard | `http://<pi-ip>` |
| Archive API | `http://<pi-ip>:8000` |
| Archive API docs | `http://<pi-ip>:8000/docs` |
| WebSocket stream | `ws://<pi-ip>:8765` |

---

## Updating

```bash
# Pull latest commits for all submodules
git submodule update --remote --merge

# Rebuild changed images
docker compose up -d --build
```

To pin submodules to a specific release rather than tracking `main`:

```bash
cd rpi-seism
git checkout v1.4.0
cd ..
git add rpi-seism
git commit -m "chore: pin daemon to v1.4.0"
```

---

## Compatibility

| stack | daemon | reader | api  | web  |
|-------|--------|--------|------|------|
| v1.x  | v1.x   | v2.x   | v1.x | v1.x |

---

## License

[GNU General Public License v3.0](LICENSE)