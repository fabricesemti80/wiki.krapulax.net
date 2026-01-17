# Repositories

This document lists the key Infrastructure-as-Code repositories that manage the homelab.

## Terraform

- `terraform/homelab-terraform-unifi` - Terraform/OpenTofu configuration to manage Unifi Dream Machine (networks, DNS records, port profiles, WiFi). Uses 1Password for credential management.

## Ansible

- `ansible/infra-ansible-home-proxmoxhosts` - Ansible automation for deploying and managing a 3-node Proxmox VE cluster with Ceph storage, Keepalived HA, NFS integration, Gmail SMTP notifications, and automated backups.

- `docker/ansible-hms-docker` - Ansible playbook for deploying the Home Media Server stack on a dedicated Docker host. Includes Plex, Jellyfin, Sonarr, Radarr, and related *arr services.

## Docker / Infrastructure

- `project-dockerlab` - Production-grade on-premises homelab based on Docker Swarm orchestration. Includes:
  - **terraform_infra/** - Phase 1: Provisions VMs on Proxmox, Cloudflare DNS, and Tunnels
  - **ansible/** - Phase 2: Configures OS, installs Docker, initializes Swarm cluster
  - **terraform_apps/** - Phase 3: Deploys core platform services via Portainer
  - **docker/** - Docker Stacks for services (Traefik, Homepage, GitLab, Beszel, etc.)
  - Uses **Doppler** for secrets management and **Cloudflare Zero Trust** for secure access

  ## Other Repositories

  The following repositories were found but are not yet documented.

  - `archive/` - (NEEDS DOCUMENTATION)
  - `kubernetes/` - (NEEDS DOCUMENTATION)
  - `nix/` - (NEEDS DOCUMENTATION)
  - `project-dockerstack-mk2/` - (NEEDS DOCUMENTATION)
