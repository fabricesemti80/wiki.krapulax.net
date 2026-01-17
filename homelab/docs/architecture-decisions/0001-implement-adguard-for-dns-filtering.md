# 1. Implement AdGuard for DNS Filtering

* **Status**: Accepted
* **Date**: 2025-12-19

## Context and Problem Statement

The homelab network requires a DNS filtering solution to block ads and malicious domains. While the UniFi router provides basic DNS services, it lacks advanced filtering capabilities. The goal is to implement a network-wide ad-blocking solution without disrupting existing internal DNS resolution for the `krapulax.home` domain, which is managed by the UniFi router.

## Decision Drivers

* **Network-wide Ad-blocking**: The primary driver is to block advertisements and trackers for all devices on the trusted networks.
* **Centralized Management**: A single, centralized ad-blocking solution is preferred over configuring individual devices.
* **Preserve Internal DNS**: The existing internal DNS resolution managed by the UniFi router must be maintained.
* **Redundancy**: The DNS setup should be resilient to the failure of a single component.

## Considered Options

1.  **Pi-hole**: A popular, self-hosted DNS sinkhole. It's a strong contender but was not chosen due to a preference for AdGuard Home's user interface and feature set.
2.  **AdGuard Home**: A self-hosted DNS sinkhole with a modern UI, extensive blocklist support, and features like DNS-over-HTTPS.
3.  **NextDNS/Cloudflare Gateway (Cloud-based)**: These services provide cloud-based DNS filtering. They were not chosen to keep DNS resolution within the local network for privacy and performance reasons.

## Decision Outcome

Chosen option: **AdGuard Home**, because it provides a powerful, self-hosted DNS filtering solution that can be configured to work in tandem with the existing UniFi router for internal DNS resolution.

### Implementation Details

*   An AdGuard Home instance (`gatekeeper-53`) is deployed as a Proxmox LXC container at `10.0.40.53`.
*   DHCP settings for trusted networks are updated to use `gatekeeper-53` as the primary DNS server.
*   AdGuard Home is configured with conditional forwarding for the `krapulax.home` domain to the UniFi router.
*   The UniFi router is configured as a secondary DNS server for fallback.