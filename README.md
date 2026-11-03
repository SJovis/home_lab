# Homelab

A home infrastructure lab built on Proxmox VE, used to host game servers for friends and to practice DevOps tooling (artifact management, CI/CD, containers, infrastructure as code, and monitoring) as part of an ongoing bootcamp.

> **Status: v0.1, baseline.** Current state documented. VLAN segmentation and automation are planned (see [Roadmap](#roadmap)).

## Goals

- Run a reliable home environment, rebuilt from scratch with better planning
- Game servers for friends: low priority, revisit only if a game comes up that changes the plans
- Practice DevOps and systems administration on real infrastructure
- Run a surveillance system for two PoE cameras in a VM on the Proxmox host (planned)
- Document design decisions, mistakes, and changes over time

## Current Topology

Internet (fibre)
│
[ISP router, 1st floor] ── NAT, DHCP, Wi-Fi (single flat network: 192.168.1.0/24 placeholder)
│
[Switch, 2nd floor]
├── Gaming / work PC (192.168.1.x)
└── HP EliteDesk (192.168.1.10) Proxmox VE host
└── Sonatype Nexus VM

Wi-Fi clients: smartphone, TV box (1st floor, feeds the TV)

The ground floor (R/C) has no lab equipment yet. A floor-by-floor layout and the planned camera extension are in [docs/network.md](docs/network.md).

**Known limitations:** all devices share one network. A compromised IoT or mobile device could reach the Proxmox host and guest VMs directly. Segmentation is planned.

## Hardware

| Device | Role | Notes |
| -------- | ------ | ------- |
| ISP router | Gateway, Wi-Fi | Provided by ISP, 1st floor |
| HP EliteDesk | Proxmox VE host | Intel Core i5-6500T (4 cores), 15.5 GiB RAM, 2 x 256 GB SSD (NVMe + SATA); 1 TB external drive for personal files, currently disconnected |
| Gaming / work PC | Workstation | Not part of the lab |
| Switch | Wired LAN | Unmanaged, 2nd floor, uplink to the router |
| PoE switch (planned) | Powers and connects the cameras | Not purchased, model `[TBD]`, connects to the router |
| 2 x PoE cameras (planned) | Surveillance | Not purchased, models `[TBD]` |

## Software

| Component | Version |
| ----------- | --------- |
| Proxmox VE | 9.1.1 |
| Sonatype Nexus | 3.97.0-13 |
| Guest OS | Ubuntu 26.04.1 LTS (all guests) |

## Guest Inventory

| VMID | Name | Type | Purpose | Status |
| ------ | ------ | ------ | --------- | -------- |
| 1004 | nexuslab | VM | Artifact repository (Nexus) | Running |

The game server (Palworld), Teamspeak and test VMs were deleted on 2026-11-01 to start from a clean slate.

## Storage and Backups

- Active Proxmox install on the NVMe SSD: LVM-thin `local-lvm` (~141 GB pool) for VM disks, and `local` directory storage on the ~69 GB root volume
- SATA SSD: unused after wiping an old test install on 2026-11-01; planned backup target
- 1 TB external drive: personal files only, not part of Proxmox storage, currently disconnected
- Backups: no scheduled backups. One manual backup of `1004 nexuslab` was taken on 2026-11-01 to `local`, which is on the same disk as the VM
- Last successful restore test: none yet

Details, the backup plan and the restore-test checklist are in [docs/storage.md](docs/storage.md).

## Security Notes

- Proxmox web UI and SSH are not exposed to the internet
- Remote access to the host is through Tailscale, not port forwarding
- Two-factor authentication is enabled on the Proxmox web UI
- No port forwarding rules are active (the old game server and Teamspeak rules were removed on 2026-11-01)
- Public IP, credentials, and host-specific secrets are excluded from this repo

## Changelog

- **v0.1**: Initial baseline documentation

## Roadmap

- [ ] Split network into management, guest, and IoT VLANs
- [ ] Add a security camera setup: two PoE cameras on a new PoE switch connected to the router, with the surveillance system running in a VM on `pve01`. Open points (recording storage, VLAN isolation, remote viewing) are in [docs/network.md](docs/network.md)

## Repository Layout

homelab/
├── README.md
├── docs/
│ ├── network.md
│ ├── storage.md
│ └── guests.md
├── configs/ # sanitized configuration files
├── lessons-learned.md
└── roadmap.md
