# Andy Alexander — Linux Systems Engineer

Multi-site Linux infrastructure lab: three Proxmox hosts across two sites, OPNsense
edge routing over XGS-PON fiber, ZFS on TrueNAS SCALE, a WireGuard overlay, and a
containerized service stack. Everything here is real and running — not a demo.

**Portfolio:** [greenbean.org](https://greenbean.org) — write-ups, selected work, resume
**Documentation:** [homelab-docs](https://github.com/greenbeanorg/homelab-docs) — 23 runbooks, ~7,900 lines, each recording what was built, why, what broke, and how to rebuild it from nothing

---

## Featured runbooks

**Full hypervisor hang — root cause analysis**
Problem: complete lockup of the primary Proxmox host.
Investigation: USB subsystem, kernel logs, and shared peripheral load traced across the host.
Root cause: a wedged USB controller shared between the UPS and other USB peripherals.
Resolution: moved UPS monitoring off the host entirely.
→ [SWEARENGEN-USB-CONTROLLER-HANG-2026-09.md](https://github.com/greenbeanorg/homelab-docs/blob/main/SWEARENGEN-USB-CONTROLLER-HANG-2026-09.md)

**Same-host VM networking failure**
Problem: one specific VM-to-VM TCP flow silently dropped.
Investigation: firewall, VLAN, FDB, and physical network state checked clean at every layer.
Finding: `vmbr0` intra-bridge forwarding behavior for that VM pair.
Status: workaround in place (routed through OPNsense); root cause still open.
→ [SWEARENGEN-VMBR0-INTRA-BRIDGE-FORWARDING-BUG-2026-09.md](https://github.com/greenbeanorg/homelab-docs/blob/main/SWEARENGEN-VMBR0-INTRA-BRIDGE-FORWARDING-BUG-2026-09.md)

**30 TB storage migration**
From mdadm RAID5 to TrueNAS SCALE / ZFS RAIDZ1, including PCIe SATA controller
passthrough, pool and dataset design, and dual SMB/NFS shares under one identity.
→ [TRUENAS.md](https://github.com/greenbeanorg/homelab-docs/blob/main/TRUENAS.md)

**Redundant DNS across separate failure domains**
Two Pi-hole resolvers, deliberately placed so neither shares a failure domain with
the other — advertised via Kea DHCPv4 option 6, with a documented static-host audit
procedure and Teleporter parity between them.
→ [DNS.md](https://github.com/greenbeanorg/homelab-docs/blob/main/DNS.md)

Full index of all 23 runbooks: [homelab-docs](https://github.com/greenbeanorg/homelab-docs)

---

## Stack

| | |
| --- | --- |
| **Linux** | Debian, Rocky, Fedora, Armbian |
| **Virtualization** | Proxmox VE, KVM/LXC |
| **Networking** | OPNsense, MikroTik RouterOS, VLANs, WireGuard, Kea DHCP |
| **DNS** | Pi-hole, Unbound |
| **Storage** | ZFS, TrueNAS SCALE, NFS/SMB |
| **Automation** | Bash, Python, Ansible, Terraform |
| **Monitoring** | Uptime Kuma, NetBox |
| **Backup** | restic, NUT |
| **Ops** | Git, Docker Compose |

## Current work

- Ansible fleet management — baseline, NUT, restic, and Docker host roles
- Extending Terraform coverage across the rest of the Proxmox fleet
- Prometheus + Grafana + node_exporter, alongside Uptime Kuma
- Rebuilding off-site restic backups following the TrueNAS/ZFS migration

---

Ormond Beach, FL · open to remote Linux systems administration / systems engineering roles
