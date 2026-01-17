# Tools

This is a curated list of tools used in the homelab infrastructure.

## Terraform / OpenTofu

Infrastructure-as-Code for managing cloud and on-premises resources.

### Providers

- [1Password provider](https://search.opentofu.org/provider/1password/onepassword/v2.1.2) - Secrets management
- [Unifi provider](https://search.opentofu.org/provider/filipowm/unifi/latest) - Network management
- [Proxmox provider](https://registry.terraform.io/providers/bpg/proxmox/latest) - VM provisioning
- [Cloudflare provider](https://registry.terraform.io/providers/cloudflare/cloudflare/latest) - DNS and Tunnels
- [Portainer provider](https://registry.terraform.io/providers/portainer/portainer/latest) - Docker Swarm management

For Terraform configuration repositories, see [Repositories](repositories.md#terraform).

## Ansible

Configuration management and automation.

- [1Password lookup](https://docs.ansible.com/projects/ansible/latest/collections/community/general/onepassword_lookup.html) - Secret retrieval
- [lae.proxmox](https://galaxy.ansible.com/lae/proxmox) - Proxmox cluster automation

For Ansible repositories, see [Repositories](repositories.md#ansible).

## Secret Management

### Doppler

Universal secrets management used by the Docker Swarm project.

- **Integration**: direnv (`.envrc` files)
- **Usage**: `eval "$(doppler env)"` loads secrets into environment
- **Repository**: [project-dockerlab](https://github.com/fabricesemti80/project-dockerlab)

### 1Password

Secret storage and retrieval via CLI.

- **Integration**: Terraform external data source, Ansible lookup
- **Usage**: `op item get "Item Name" --vault "Vault" --format json`
- **Repository**: [homelab-terraform-unifi](https://github.com/fabricesemti80/homelab-terraform-unifi)

For details on how 1Password is integrated, see the [1Password Usage Guide](tools/1password_usage.md).

## Tailscale

Mesh VPN for secure remote access across devices and networks.

- **DNS**: Configured to use Quad9 with split DNS for `krapulax.home`
- **Details**: See [Tailscale Configuration](tools/tailscale.md)

## Task (Taskfile)

Task-based command runner used across all repositories.

- **Website**: https://taskfile.dev/
- **Usage**: `task --list` to see available commands
- **Common tasks**: `task init`, `task plan`, `task apply`

## direnv

Automatic environment variable loading based on directory.

- **Website**: https://direnv.net/
- **Usage**: Place secrets in `.envrc`, run `direnv allow`
- **Integration**: Works with Doppler for automatic secret injection

## Cloudflare

DNS, CDN, and Zero Trust access.

- **DNS**: Manages `krapulax.dev` / `krapulax.net` domain
- **Tunnels**: Secure ingress without open firewall ports
- **Zero Trust**: Email OTP/SSO authentication for external access
