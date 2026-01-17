# Hardware

This document outlines the hardware components of the homelab.

## Physical Infrastructure

The list includes only devices set up with fixed IPs or holding critical roles. Consumer devices (phones, laptops, Alexa, etc.) are excluded.

### Servers

- **QNAP NAS**
  - **Role**: NFS Storage
  - **OS**: QTS
  - **IP**: `10.0.40.2`
  - **Notes**: Provides NFS for Proxmox (ISO, Backup, Templates), Docker data, Media files. Ports: 80, 443, 22.

- **Proxmox Nodes (x3)**
  - **Role**: Hypervisor + Ceph Storage
  - **Hardware**: Minisforum Mini PCs (NVMe + dual NIC)
  - **OS**: Proxmox VE (Debian 12)
  - **Management IPs**: `10.0.40.10`, `10.0.40.11`, `10.0.40.12` (VLAN 40)
  - **Ceph IPs**: `10.0.70.10`, `10.0.70.11`, `10.0.70.12` (VLAN 70)
  - **HA VIP**: `10.0.40.15` (Keepalived floating IP for GUI access)
  - **Notes**: Hosts VMs and Containers. Dual-homed: vmbr0 (management with internet) and eth0 (Ceph-only, no gateway).

- **Docker Media Server**
  - **Role**: Home Media Server (HMS) Stack
  - **Hardware**: Proxmox VM
  - **OS**: Ubuntu
  - **IP**: `10.0.40.30`
  - **Notes**: Runs Plex, Jellyfin, Sonarr, Radarr, and *arr stack via ansible-hms-docker.
  - **DNS**: This stack uses the `krapulax.dev` domain currently.

- **Docker Swarm Cluster (x3)**
  - **Role**: Container Orchestration
  - **Hardware**: Proxmox VMs
  - **OS**: Ubuntu
  - **IPs**: `10.0.30.21`, `10.0.30.22`, `10.0.30.23` (DEV-INFRA VLAN 30)
  - **Swarm VIP**: `10.0.40.40`
  - **Notes**: Docker Swarm managers running platform services (Traefik, Portainer, Homepage, GitLab, etc.) via project-dockerlab.
  - **DNS**: This stack uses the `krapulax.net` domain currently.

### Network Gear

- **UDM-PRO**
  - **Model**: Unifi Dream Machine Pro
  - **Role**: Router / Firewall / Gateway / DNS
  - **Connections**:
    - **Gateway**: `.1` on ALL ranges.
    - **Uplink**: ISP (YourFibre).
    - **Downlinks**: Unifi 24P PoE, QNAP NAS, 2x Pi Zeros.
  - **Services**: DHCP, Firewall, Routing (DNS provided by NextDNS).
  - **Ports**: 80, 443, 22 open.

- **Main Switch**
  - **Model**: Unifi 24P PoE
  - **Role**: Core Switch
  - **Connections**:
    - **Uplink**: UDM-PRO.
    - **Downlink**: Unifi 8P PoE.
    - **PoE Devices**: 3x Cameras, 1x Doorbell, 1x Chime, 2x WAPs.

- **Cluster Switch**
  - **Model**: Unifi 8P PoE
  - **Role**: Distribution Switch
  - **Connections**:
    - **Uplink**: Unifi 24P PoE.
    - **Downlinks**: 3x Proxmox Nodes (dual-homed for management + Ceph).
