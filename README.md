# RYSEN Hytera UDP Proxy

Docker image for the [RYSEN](https://github.com/ShaYmez/RYSEN) stack. One public Hytera IP Multi-site Connect port triple, then further triples for repeaters behind the same address.

**Image:** `shaymez/rysen-sp-hyt:latest`

The proxy sources live in RYSEN. This repo copies them. Until the Hytera branch merges, sync from `feature/HYTERA`. After that merge, change the sync workflow ref to `master`.

## Docker

Copy `sync/hytera-proxy-SAMPLE.cfg` to the host (for example `/etc/rysen/hytera-proxy.cfg`) and set `MASTER` to the RYSEN container on the compose network.

```yaml
hytera-proxy:
    container_name: hytera-proxy
    image: shaymez/rysen-sp-hyt:latest
    volumes:
        - '/etc/rysen/hytera-proxy.cfg:/opt/rysen-sp-hyt/hytera-proxy.cfg'
    ports:
        - '50000-50029:50000-50029/udp'
    restart: unless-stopped
    depends_on:
        - rysen
    networks:
        app_net:
            ipv4_address: 172.16.238.31
    read_only: true
```

`50000-50029` matches `SLOTS = 10` (three ports per repeater). Widen the published range if `SLOTS` grows.

## Configuration

| Key | Purpose |
|-----|---------|
| `MASTER` | Backend RYSEN address (`172.16.238.10` on the installer network) |
| `P2P_PORT` | Public master UDP port (usually 50000) |
| `PUBLIC_SLOT_START` | First public port triple |
| `BACKEND_SLOT_START` | First backend port triple on RYSEN |
| `SLOTS` | Number of repeater triples |
| `TIMEOUT` | Idle session timeout (seconds) |

CPS for the first repeater uses master/P2P **50000**, voice **50001**, and RDAC **50002**. A second repeater behind the same public address starts at **50003**.
