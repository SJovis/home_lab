# Homelab

A home infrastructure lab built on Proxmox VE, used to host game servers for friends and to practice DevOps tooling (artifact management, CI/CD, containers, infrastructure as code, and monitoring) as part of an ongoing bootcamp.

> **Status: v0.1, baseline.** Current state documented. VLAN segmentation and automation are planned (see [Roadmap](#roadmap)).

## Goals

- Run a reliable home environment for game servers shared with friends
- Practice DevOps and systems administration on real infrastructure
- Document design decisions, mistakes, and changes over time

## Current Topology

Internet (fibre)
│
[ISP router] ── NAT, DHCP, Wi-Fi (single flat network: 192.168.1.0/24 placeholder)
│
[Switch, 2nd floor]
├── Gaming / work PC (192.168.1.x)
└── HP EliteDesk (192.168.1.10) Proxmox VE host
├── Game server VMs
├── Teamspeak VM
└── Sonatype Nexus VM

Wi-Fi clients: smartphone, smart TV

**Known limitations:** all devices share one network. A compromised IoT or mobile device could reach the Proxmox host and guest VMs directly. Segmentation is planned.

## Hardware

| Device | Role | Notes |
| -------- | ------ | ------- |
| ISP router | Gateway, Wi-Fi | Provided by ISP |
| HP EliteDesk | Proxmox VE host | [CPU / RAM / storage to fill in] |
| Gaming / work PC | Workstation | Not part of the lab |
| Switch | Wired LAN | Unmanaged, 2nd floor |

## Software

| Component | Version |
| ----------- | --------- |
| Proxmox VE | 8.x |
| Sonatype Nexus | [version] |
| Guest OS | [e.g. Ubuntu Server 22.04] |

## Guest Inventory

| VMID | Name | Type | Purpose | Status |
| ------ | ------ | ------ | --------- | -------- |
| 100 | nexus | VM | Artifact repository (Nexus) | Running |
| 101 | game-server-1 | VM | Friends' game server | Offline |
| 102 | ts-server | VM | Teamspeak voice server | Offline |

## Storage and Backups

- Local storage on External Hard-Drive
- Backups [ not yet configured ]
- Last successful restore test: none yet

## Security Notes

- Proxmox web UI and SSH are not exposed to the internet
- Public IP, credentials, and host-specific secrets are excluded from this repo

## Changelog

- **v0.1**: Initial baseline documentation

## Roadmap

- [ ] Split network into management, guest, and IoT VLANs
- [ ] Add a security cameras setup to the network. Plan is to have them connected to a PoE Switch that connects to the Router.

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
