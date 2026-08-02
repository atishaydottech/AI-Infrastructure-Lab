# AI Infrastructure Lab

## 🏗️ Architecture Overview

Comprehensive self-hosted homelab on **Proxmox VE** (Minisforum UM760) with three VMs:
- **VM100**: Core infrastructure (Caddy, monitoring, container management)
- **VM200**: K3s Kubernetes cluster (coming soon)
- **VM300**: AI/ML workloads - Project Alpha (coming soon)

## 🔐 Security & Access

### Tailscale VPN
- Private mesh network: `100.x.x.0/10`
- All services accessible only via Tailscale peers
- No direct internet exposure

### HTTPS with Let's Encrypt
- Caddy reverse proxy with automatic TLS certificates
- DNS-01 challenges via Cloudflare API
- Certificates auto-renewed

### SSH Authentication
- ED25519 key-based authentication (no passwords)
- Fail2Ban + UFW firewall
- SSH hardening enabled

## 📋 VM100 Services

| Service | URL | Purpose |
|---------|-----|---------|
| Portainer | `https://portainer.[DOMAIN]` | Container management UI |
| Uptime Kuma | `https://uptime.[DOMAIN]` | Service monitoring & status |
| Prometheus | `https://prometheus.[DOMAIN]` | Metrics collection |
| Grafana | `https://grafana.[DOMAIN]` | Metrics visualization |

## 🔧 Docker Compose Structure

/opt/compose/
├── caddy/ # Reverse proxy + TLS
├── portainer/ # Container management
├── uptime-kuma/ # Uptime monitoring
└── monitoring/ # Prometheus + Grafana + cAdvisor

## 💾 Backups

- **Location**: `/opt/backups/`
- **Frequency**: Daily at 2:00 AM
- **Retention**: 7 days

## 🚀 Quick Commands

```bash
ssh core-infra "docker ps"
ssh core-infra "cd /opt/compose/caddy && docker compose logs caddy | tail -20"
```

## ⚙️ Configuration Files

### Secrets Management
- Cloudflare API token: Set via `CF_API_TOKEN` environment variable
- All real IPs/domains replaced with placeholders before committing

## 🔍 Troubleshooting

1. Check Tailscale connection: `tailscale status`
2. Verify service running: `docker ps | grep [service]`
3. Check Caddy logs: `cd /opt/compose/caddy && docker compose logs`

## 🛠️ Next Steps

1. **VM200 (K3s)**: Set up Kubernetes cluster
2. **VM300 (AI/ML)**: Deploy AI workloads with GPU support
3. **Logging**: Add Loki + Promtail for centralized logs

## ⚠️ Security Checklist

- [ ] Cloudflare API token rotated regularly
- [ ] SSH keys backed up securely
- [ ] Grafana password changed from default
- [ ] No real IPs/domains in version control
- [ ] Backup restoration tested monthly

---

**Status**: ✅ VM100 Core Complete
