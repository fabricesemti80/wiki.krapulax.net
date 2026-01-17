# Services

This document provides an overview of the services running in the homelab.

## Network Services

- **DHCP**
  - Provided by UDM-PRO
  - Scope: All VLANs

- **DNS**
  - **Upstream**: NextDNS (ad-blocking, privacy, security filtering)
  - **Public Domains**: `krapulax.dev` & `krapulax.net` (managed by Cloudflare)

- **VPN**
  - **Tailscale**: Mesh VPN for remote access

## Storage Services

- **NFS**
  - Provided by QNAP NAS (`10.0.40.2`)
  - Exports:
    - `/swarm-appdata` - Docker Swarm application data *(legacy / to be deleted)*
    - `/proxmox-template` - Proxmox VM templates
    - `/proxmox-iso` - Proxmox ISO images
    - `/proxmox-backup` - Proxmox backup storage
    - `/portainer-appdata` - Portainer application data *(legacy / to be deleted)*
    - `/media` - Media library
    - `/docker-appdata` - Docker application data *(legacy / to be deleted)*
    - `/data` - General data *(legacy / to be deleted)*

- **Ceph**
  - Distributed storage provided by 3x Proxmox nodes
  - Network: `10.0.70.0/24` (VLAN 70)
  - Pools:
    - `ceph-proxmox-rbd` - Primary VM/container storage (RBD)
    - `ceph-proxmox-fs` - CephFS for snippets and shared data
  - Features: High availability, automatic failover

## Virtualization & Infrastructure

- **Proxmox VE**
  - 3-node cluster for compute and Ceph storage
  - HA VIP: `10.0.40.15` (Keepalived)
  - Automated backups via NFS
  - Gmail SMTP for notifications

- **Docker Swarm**
  - 3-manager cluster for platform services
  - Orchestration for HA container workloads
  - Managed via Portainer

## Applications

### Docker Swarm Platform (`project-dockerlab`)

Services running on the Docker Swarm cluster, exposed via Cloudflare Tunnels:

| Service | Description | Access |
|---------|-------------|--------|
| **Traefik** | Ingress controller and reverse proxy | Internal |
| **Portainer** | Docker Swarm management UI | `portainer.krapulax.net` |
| **Homepage** | Dashboard | `homepage.krapulax.net` |
| **GitLab** | Git repository hosting | `*.krapulax.net` |
| **Beszel** | Monitoring agent | Internal |
| **Gatus** | Status monitoring | Internal |
| **Glance** | Service overview | Internal |
| **Filebrowser** | Web file manager | Internal |
| **Dockpeek** | Docker container monitoring | `dockpeek.krapulax.net` |
| **Cloudflared** | Cloudflare Tunnel agent | Internal |

### Home Media Server Stack (`docker/ansible-hms-docker`)

Services running on the dedicated media server VM (`10.0.40.30`):

| Service | Status | Description |
|---------|--------|-------------|
| **Plex** | ✅ | Media streaming server |
| **Jellyfin** | ✅ | Open-source media server |
| **Sonarr** | ✅ | TV show management |
| **Radarr** | ✅ | Movie management |
| **Sabnzbd** | ✅ | Usenet download client |
| **Overseerr** | ✅ | Media request management |
| **Traefik** | ✅ | Reverse proxy with SSL |
| **Homepage** | ✅ | Dashboard |
| Prowlarr | ✅  | Indexer management |
| Bazarr | ⬜ | Subtitle management |
| Lidarr | ⬜ | Music management |
| Readarr | ⬜ | Book management |
| Tautulli | ⬜ | Plex statistics |
| Authentik | ⬜ | SSO provider |

### Infrastructure Services

| Service | Host | Description |
|---------|------|-------------|
| **NextDNS** | External (Cloud) | Network-wide DNS filtering and ad-blocking |
| **Proxmox Backup** | NFS | Automated VM backups |

## Ingress & Access

### Cloudflare Zero Trust

External access is provided via Cloudflare Tunnels with Zero Trust authentication:

```
User → Cloudflare Edge → Zero Trust Auth → Cloudflared → Traefik → Service
```

- **Authentication**: Email OTP / SSO
- **Bypass policies**: GitLab (public), Beszel (agent telemetry)

### Domain Access

- **Media Server**: `krapulax.dev`
- **Docker Swarm**: `krapulax.net`
