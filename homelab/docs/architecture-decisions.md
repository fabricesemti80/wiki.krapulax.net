# Architecture Decisions & Roadmap

This document tracks key architectural decisions, their context, and the future roadmap for the `krapulax` homelab.

## Decisions

### 1. DNS Strategy - NextDNS with Cloudflare

* **Decision**: Use **NextDNS** (external cloud service) as the sole upstream DNS for ad-blocking and privacy. Public domains (`krapulax.dev` and `krapulax.net`) are managed via **Cloudflare**.
* **Context**: Previously used self-hosted AdGuard Home and Unifi for internal DNS, but switched to a unified NextDNS/Cloudflare model to simplify the network and remove reliance on internal-only domains.
* **Status**: ✅ Deployed.
* **Benefits**:
  - No self-hosted infrastructure to maintain
  - Centralized public DNS management via Cloudflare
  - Built-in analytics and blocking via NextDNS dashboard

### 2. Secret Management - Multi-Provider Approach

* **Decision**: Use multiple secret management providers based on context:
  - **1Password**: For Terraform configurations (via `op` CLI) - used in `terraform/homelab-terraform-unifi`
  - **Doppler**: For Docker Swarm and application secrets - used in `project-dockerlab`
  - **Environment files**: For local development and Ansible credentials
* **Context**: Different tooling requires different secret management approaches. 1Password works well with Terraform's external data source, while Doppler integrates seamlessly with direnv and Docker environments.
* **Status**: ✅ Implemented across repositories.

### 3. Storage Architecture - Hybrid Approach

* **Decision**: Use a hybrid storage architecture:
  - **Ceph**: For "hot" VM/container storage (high availability, automatic failover)
  - **NFS (QNAP)**: For bulk storage (media), backups, ISOs, and templates
* **Context**: The QNAP NAS was previously a SPOF. By using Ceph for live application data, we achieve HA while retaining NFS for cost-effective bulk storage.
* **Status**: ✅ Ceph cluster deployed on 10.0.70.0/24 network.

### 4. Container Orchestration - Docker Swarm

* **Decision**: Use Docker Swarm for platform services instead of Kubernetes.
* **Context**: Docker Swarm provides sufficient orchestration for the homelab use case with significantly less complexity than Kubernetes. The 3-manager cluster provides HA without the overhead of etcd management.
* **Status**: ✅ Deployed via project-dockerlab.

### 5. External Access - Cloudflare Zero Trust

* **Decision**: Use Cloudflare Tunnels with Zero Trust authentication for external access.
* **Context**: This eliminates the need for open ports on the firewall and provides enterprise-grade authentication (email OTP/SSO) without self-hosting additional infrastructure.
* **Status**: ✅ Deployed for all public services.

### 6. Proxmox High Availability

* **Decision**: Use Keepalived with VRRP for Proxmox GUI high availability.
* **Context**: A floating VIP (`10.0.40.15`) ensures consistent access to the Proxmox web UI regardless of which node is master.
* **Status**: ✅ Deployed via `ansible/infra-ansible-home-proxmoxhosts`.

## Roadmap / Todo List

### Short Term (Immediate Improvements)

- [ ] **NAS Backup**: Implement a "Cold Backup" for the QNAP NAS (e.g., large external USB drive) to mitigate total data loss risk

### Medium Term (Architecture Evolution)

- [ ] **Media Server Migration**: Consider migrating `docker/ansible-hms-docker` services to Docker Swarm for unified management
- [ ] **Monitoring Stack**: Deploy Prometheus/Grafana or similar for centralized monitoring
- [ ] **Log Aggregation**: Deploy Loki or similar for centralized logging

### Long Term (Scaling)

- [x] **DNS Filtering**: Migrated to NextDNS for network-wide filtering
- [x] **Docker Swarm**: Platform services running on HA Swarm cluster
- [x] **Ceph Storage**: Distributed storage operational
- [ ] **GitOps**: Full GitOps workflow with ArgoCD or FluxCD (if migrating to Kubernetes)
