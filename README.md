# greenbeanorg — Andy Alexander's Homelab

Multi-site homelab infrastructure, documented as production-style runbooks. I'm a Linux systems administrator and this is where I design, break, fix, and document infrastructure.

**Everything here is real and running** — three Proxmox hosts across two sites, ZFS storage on TrueNAS SCALE, OPNsense edge routing over XGS-PON fiber, automated verified backups, and a containerized service stack.

## Repositories

| Repo | What it is |
|---|---|
| [homelab-docs](https://github.com/greenbeanorg/homelab-docs) | Runbooks and design docs: storage, backup, networking, power, services |
| [homelab](https://github.com/greenbeanorg/homelab) | Sanitized configs: Docker Compose stacks, NUT, restic scripts, tooling |

## Network at a glance

```mermaid
flowchart TB
    INET((Internet<br/>XGS-PON Fiber))
    ONT[XGS-PON ONT-on-a-stick<br/>SFP+ module]

    subgraph SW [MikroTik CRS310-8G+2S+ — RouterOS]
        BW[bridge-WAN<br/>SFP+ cage + 1 port]
        BL[bridge-LAN<br/>remaining ports]
    end

    subgraph WU [wu — Proxmox host, ODROID-H3]
        FW[OPNsense VM<br/>Router / Firewall]
    end

    subgraph LAN [Local Network]
        PH[Pi-hole DNS<br/>ODROID-XU4]
        SW1[swearengen<br/>Proxmox Host + TrueNAS SCALE VM<br/>ZFS RAIDZ1 pool — PCIe SATA passthrough]
        FARN[farnum<br/>Plex Media Server VM over NFS]
        HA[Home Assistant VM]
    end

    subgraph REMOTE [Remote Site]
        RPX[Remote Proxmox<br/>Pi-hole + LinuxGSM game server]
    end

    VPS[vps1<br/>Off-site restic backup target — SFTP]

    INET --- ONT --- BW
    BW ---|"wu NIC 1 → WAN vNIC"| FW
    FW ---|"LAN vNIC → wu NIC 2"| BL
    BL --- LAN
    LAN -. WireGuard/SSH .- REMOTE
    LAN -. restic over SFTP .- VPS
```

**Traffic path:** fiber terminates on the ONT SFP+ in the switch's isolated **bridge-WAN**, which hands off to a dedicated NIC on `wu`; the OPNsense VM routes/firewalls and sends LAN-bound traffic out a second NIC back into the switch's **bridge-LAN** — router-on-a-VM with a physical hairpin through the CRS310.

## Current projects

- **Ansible fleet management** — converting host configuration (baseline, NUT, restic, Docker hosts) to roles across all sites
- **Monitoring modernization** — Prometheus + Grafana + node_exporter fleet-wide
- **VLAN segmentation** — redesigning the flat L2 network into trust zones on the CRS310

## Recently completed

- 30TB storage migration: mdadm RAID5 → TrueNAS SCALE / ZFS RAIDZ1 with PCIe SATA passthrough ([runbook](https://github.com/greenbeanorg/homelab-docs))
- SSHFS → NFS for all cross-host storage access
- Automated restic backups across all sites with 90-day retention and scheduled integrity verification
- PowerPanel (pwrstat) → NUT UPS monitoring conversion (in progress, one host complete)
- Beta tester for XGS-PON ONT-on-a-stick ([pon.wiki guide](https://pon.wiki/guides/masquerade-as-the-att-inc-bgw320-500-505-with-the-was-110/))

---
📫 andy.alexander@gmail.com · Ormond Beach, FL · open to remote systems/NOC roles
