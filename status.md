# Status

**Last updated:** 2026-10-31
**Current version:** v0.1 (baseline)

## Environment

- Host: HP EliteDesk running Proxmox VE 8.x (hostname: pve01)
- Network: single flat network behind ISP router (192.168.1.0/24 placeholder), wired and Wi-Fi clients
- Guests:
  - 100 `nexus`: Sonatype Nexus artifact repository (running)
  - 101 `game-server-1`: friends' game server (offline)
  - 102 `ts-server`: Teamspeak voice server (offline)
- Storage: local storage on external hard drive
- Backups: not yet configured
- Known limitation: all devices share one network, so a compromised IoT or mobile device can reach the Proxmox host and guests

## Goals (current)

- Run a reliable home environment for game servers shared with friends
- Practice DevOps and systems administration on real infrastructure
- Document design decisions, mistakes, and changes over time

## Done

- [x] Proxmox VE installed on EliteDesk
- [x] Sonatype Nexus running in a VM
- [x] Game server and Teamspeak VMs created
- [x] v0.1 README drafted and updated with current topology and guests

## In Progress

- [ ] Fill in README placeholders: EliteDesk CPU/RAM/storage, Nexus version, guest OS
- [ ] Confirm Proxmox web UI and SSH are not reachable from outside the network
- [ ] Enable 2FA on Proxmox admin accounts
- [ ] Set up pre-commit secret scanning (gitleaks)

## Next

- [ ] Plan VLAN segmentation (management, guest, IoT) and pick VLAN IDs and subnets
- [ ] Plan security camera setup: PoE switch connected to the router
- [ ] Write `docs/network.md` with the current and target topology
- [ ] Start `lessons-learned.md` with the first real issue encountered

## Open Questions

- Does the ISP router support VLANs, or is a managed switch needed for segmentation?
- Which PoE switch fits the camera plan, and how many ports are needed?
- Should the external hard drive be the long-term home for backups, or only for VM storage?
- Should friends connect via WireGuard instead of port forwarding?

## Decisions Log

- 2026-10-31: Chose flat network for v0.1 baseline; segmentation deferred to v0.2
- 2026-10-31: Public repo with sanitized configs; secrets kept out of git

## Session Notes

- 2026-10-31: README updated. Goals revised. Next: fill in hardware specs and plan the VLAN layout.
