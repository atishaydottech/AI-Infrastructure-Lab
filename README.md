# AI Infrastructure Lab

A comprehensive homelab infrastructure built on Proxmox, featuring containerized services, Kubernetes-ready architecture, and AI/ML workloads.

## Hardware Overview

**Mini PC:** Minisforum UM760
- **CPU:** AMD Ryzen 5 7600 (6-core)
- **RAM:** 16 GB
- **Storage:** 1 TB SSD
- **Hypervisor:** Proxmox VE
- **Network:** Tailscale VPN + SSH remote access
- **Management:** MacBook (CLI via SSH)

## Network Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Home Network (192.168.1.0/24)             │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Gateway: 192.168.1.1                                        │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Proxmox Host (pve)                                  │   │
│  │  LAN: 192.168.1.10                                   │   │
│  │  Tailscale: 100.66.237.80                            │   │
│  │  Web UI: https://192.168.1.10:8006                   │   │
│  │                                                        │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐ │   │
│  │  │   VM100     │  │   VM200     │  │    VM300     │ │   │
│  │  │ core-infra  │  │     k3s     │  │ project-alpha│ │   │
│  │  │             │  │             │  │              │ │   │
│  │  │ 192.168.1.x │  │ 192.168.1.x │  │ 192.168.1.x  │ │   │
│  │  │ 100.98.x.x  │  │ 100.xx.x.x  │  │ 100.xx.x.x   │ │   │
│  │  └─────────────┘  └─────────────┘  └──────────────┘ │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Tailscale Network (100.64.0.0/10)                   │   │
│  │  Private mesh VPN for all services                   │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### DNS & Domains

- **Domain:** ibn5100.dev
- **Registrar:** Namecheap
- **DNS Host:** Cloudflare (Free plan)
- **Strategy:** All services private over Tailscale, wildcard DNS via Cloudflare

## Virtual Machine Layout

### VM100: core-infra
**Purpose:** Core infrastructure, container orchestration, reverse proxy

**Specs:**
- OS: Ubuntu 26.04 LTS
- LAN IP: 192.168.1.129
- Tailscale IP: 100.98.137.2
- SSH Alias: `ssh core-infra`
- User: atishay

**Installed Services:**
- Docker Engine + Docker Compose
- Portainer CE (container management)
- Caddy (reverse proxy with wildcard TLS)
- Uptime Kuma (uptime monitoring)
- Tailscale VPN client
- UFW (firewall)
- Fail2Ban (intrusion prevention)
- Unattended-upgrades (security patches)

**Accessibility:**
- Portainer: https://portainer.ibn5100.dev
- Uptime Kuma: https://uptime.ibn5100.dev

**Filesystem:**
```
/opt/
├── backups/          # Container backup snapshots
├── compose/          # Docker Compose projects
│   ├── caddy/        # Reverse proxy + TLS
│   ├── uptime-kuma/  # Uptime monitoring
│   └── ...           # Other services
└── data/             # Persistent application data
```

### VM200: k3s (Planned)
**Purpose:** Kubernetes cluster for distributed workloads

**Planned Stack:**
- K3s (lightweight Kubernetes)
- ArgoCD (GitOps continuous deployment)
- LiteLLM (LLM API gateway)
- Ollama (local LLM inference)
- Qdrant (vector database)
- PGVector (PostgreSQL vector extension)

### VM300: project-alpha (Planned)
**Purpose:** AI/ML project deployment

**Planned Stack:**
- OCR service (document scanning)
- Document ingestion pipeline
- RAG (Retrieval Augmented Generation)
- REST API backend
- Frontend UI
- Edge workers (async processing)

## Security Posture

✅ **Completed:**
- UFW firewall (deny incoming, allow SSH)
- Fail2Ban rate limiting
- SSH key-based authentication (no passwords)
- Automatic security updates (unattended-upgrades)
- Root SSH login disabled
- Tailscale VPN for private access

⏳ **Next Steps:**
- Regular backup automation
- Container image scanning
- Network segmentation

## Project Status

### VM100: DONE ✅
- [x] Proxmox setup and network config
- [x] Ubuntu base installation
- [x] Docker & Compose
- [x] Portainer deployment
- [x] Tailscale connectivity
- [x] SSH hardening
- [x] Caddy reverse proxy with Cloudflare DNS
- [x] Uptime Kuma monitoring
- [x] Timezone configuration

### VM200: NOT STARTED
- [ ] VM creation & network setup
- [ ] K3s cluster initialization
- [ ] ArgoCD GitOps setup
- [ ] LiteLLM + Ollama deployment
- [ ] Vector database setup

### VM300: NOT STARTED
- [ ] VM creation & base OS
- [ ] Docker runtime setup
- [ ] Service scaffolding

## Development Workflow

### SSH Access

From your Mac:
```bash
ssh core-infra                          # Via SSH config alias
ssh atishay@100.98.137.2                # Via Tailscale
ssh atishay@192.168.1.129               # Via LAN (if on network)
```

### Key Files & Directories

**On VM100:**
- `/opt/compose/caddy/` — Caddy config + TLS certificates
- `/opt/compose/uptime-kuma/` — Uptime Kuma container setup
- `/opt/data/` — Persistent volumes
- `~/.ssh/authorized_keys` — SSH key-based auth (passwordless)

**Git Repo Structure:**
```
AI-Infrastructure-Lab/
├── README.md                    # This file
├── proxmox/                     # Proxmox configs & scripts
├── vm1-core-infra/
│   ├── docker-compose.yml      # Core services
│   ├── caddy/Caddyfile         # Reverse proxy config
│   └── scripts/                # Setup & maintenance
├── vm2-k3s/                    # K3s configuration
└── vm3-project-alpha/          # Project Alpha source
```

## Secrets & Environment

🔒 **Never commit:**
- `.env` files with API tokens
- Cloudflare API tokens
- SSH private keys
- Credentials or passwords

**Secrets Management:**
- Cloudflare API tokens stored locally in `.env` (gitignored)
- Sensitive config passed via Docker secrets or environment files
- All `.env*` files in `.gitignore`

## Next Steps

**Short term (this week):**
1. Finalize VM100 with backup automation
2. Create golden-vm1 Proxmox snapshot
3. Start VM200 (K3s) setup

**Medium term (next 2 weeks):**
4. Deploy K3s cluster
5. Set up ArgoCD for GitOps
6. Deploy LiteLLM + Ollama

**Long term:**
7. Complete VM300 with Project Alpha
8. Implement CI/CD pipeline
9. Add monitoring dashboard (Grafana/Prometheus)

## Useful Commands

**Proxmox:**
```bash
ssh root@192.168.1.10                  # Proxmox host
# Web UI: https://192.168.1.10:8006
```

**VM100:**
```bash
ssh core-infra

# Check Docker status
docker ps
docker compose ps  # in /opt/compose/*/

# View logs
docker logs container-name
docker compose logs -f

# Restart services
docker compose restart  # in service directory
```

**Tailscale:**
```bash
# On VM100
tailscale status
tailscale ip -4

# Test connectivity
ping 100.98.137.2  # VM100 Tailscale IP
```

## References

- **Proxmox Docs:** https://pve.proxmox.com/
- **Docker Compose:** https://docs.docker.com/compose/
- **Caddy:** https://caddyserver.com/
- **Cloudflare DNS:** https://dash.cloudflare.com
- **Tailscale:** https://tailscale.com/
- **K3s Docs:** https://docs.k3s.io/

## License

Personal homelab project.

---

**Last Updated:** August 2, 2026  
**Maintainer:** Atishay
