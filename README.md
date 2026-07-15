# Network Infrastructure Portfolio

Design, build, and operation of a production-grade network and virtualization
environment supporting live users and an isolated external-client workspace.

**Live docs:** [uplinksecurity.net/infrastructure](https://uplinksecurity.net/infrastructure)

> Network addressing in this repository is illustrative and does not reflect
> live production values.

---

## Stack

`Proxmox VE` · `UniFi / UDM Pro` · `VLAN segmentation` · `Dual-WAN failover`
`Docker` · `ZFS` · `NFS` · `Active Directory` · `Cloudflare Zero Trust`
`Prometheus` · `Grafana` · `Nginx` · `Linux` · `Python`

## Architecture

- **Network** — Routed, VLAN-segmented LAN behind a UDM Pro. Dual-WAN with
  automatic failover. Default-deny between client and infrastructure segments.
- **Virtualization** — Proxmox VE on bare metal. Tiered storage: LVM-thin and
  ZFS on NVMe for VM disks, ZFS mirror for bulk, NFS export to the container host.
- **Services** — 20+ containerized workloads via Docker Compose, reverse-proxied
  through Nginx.
- **Access** — Three-layer auth. Zero inbound ports; all external traffic arrives
  over an outbound-only Cloudflare Tunnel with per-application identity policies.
- **Observability** — Prometheus + Grafana across host, container, network, and
  synthetic endpoint targets. 30-day retention.

## Selected engineering problems

| Problem | Resolution |
|---|---|
| Firewall rules silently bypassed by a vendor isolation feature | Traced the actual packet path rather than trusting the UI; injected persistent gateway iptables rules with on-boot reapplication |
| Cascading outage from a single storage transport fault | Root-caused the dependency chain (bus drop → read-only remount → stale NFS handle → container failure); built a recovery runbook and a device-ready startup gate |
| Database corruption after hard power loss | Authored graceful shutdown sequencing and automated dependency-ordered boot recovery ahead of a known grid outage |
| Container on macvlan unreachable from its own host | Identified macvlan host isolation as by-design; redesigned the integration path |
| DNS resolver conflict on port 53 | Disabled the systemd-resolved stub; handed authority to the filtering resolver |

## Client deployment

Isolated Windows workspace delivered to an external contractor working from
another country — dedicated VLAN, browser-based RDP via Guacamole, identity-scoped
Cloudflare Access policy. No VPN client, no exposed RDP, no inbound ports.

## Contents

| File | Description |
|---|---|
| `infrastructure.html` | Full technical documentation |
| `index.html` | Portfolio landing page |
