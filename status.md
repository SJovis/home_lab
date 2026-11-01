# Status

**Last updated:** 2026-11-01
**Current version:** v0.1 (baseline)

## Environment

- Host: HP EliteDesk running Proxmox VE 9.1.1 (hostname: pve01)
- Network: single flat network behind ISP router (192.168.1.0/24 placeholder), wired and Wi-Fi clients
- Guests (all Ubuntu 26.04.1 LTS):
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
- [x] README placeholders filled in (hardware, Nexus 3.97.0-13, Ubuntu 26.04.1 LTS guests, Proxmox 9.1.1)
- [x] 2FA enabled on Proxmox (TOTP plus recovery keys), 2026-11-01
- [x] Port forwarding rules for game server and Teamspeak removed (2026-11-01)
- [x] Pre-commit secret scanning (gitleaks) via tracked `.githooks/pre-commit`

## In Progress

- [ ] Confirm Proxmox web UI and SSH are not reachable from outside the network

## Next

- [ ] Plan VLAN segmentation (management, guest, IoT) and pick VLAN IDs and subnets
- [ ] Plan security camera setup: PoE switch connected to the router
- [ ] Write `docs/network.md` with the current and target topology
- [ ] Start `lessons-learned.md` with the first real issue encountered

## Open Questions

- Router support for VLANs is unconfirmed. Is a dedicated router (VyOS/OPNsense on a small PC) plus a managed switch worth it for segmentation?
- Which PoE switch fits the camera plan, and how many ports are needed?
- Should the external hard drive be the long-term home for backups, or only for VM storage?
- Should friends connect via WireGuard instead of port forwarding?

## Decisions Log

- 2026-10-31: Chose flat network for v0.1 baseline; segmentation deferred to v0.2
- 2026-10-31: Public repo with sanitized configs; secrets kept out of git
- 2026-11-01: Pre-commit hook lives in tracked `.githooks/` (enabled per clone with `git config core.hooksPath .githooks`) so it is versioned with the repo

## Session Notes

- 2026-10-31: README updated. Goals revised. Next: fill in hardware specs and plan the VLAN layout.
- 2026-11-01: Added gitleaks pre-commit hook (`.githooks/pre-commit`, scans staged changes). Verified it blocks a fake token and allows clean commits. CLAUDE.md was already committed.
- 2026-11-01: README hardware and Nexus version filled in from the Proxmox summary page. Notes: Tailscale is installed on the host for remote access; Teamspeak was only a trial; game server (Palworld, ~6 users) is idle; ports were forwarded on the router for those guests; no backups, 2FA not yet enabled.
- 2026-11-01: Confirmed Proxmox VE 9.1.1 (docs said 8.x, corrected) and Ubuntu 26.04.1 LTS on all guests. Port forwarding for game server/Teamspeak removed. Game server last hosted Palworld (~6 users).
- 2026-11-01: Proxmox 2FA enabled and tested (TOTP plus recovery keys). Still to do: 2FA on the Tailscale account, confirm Proxmox is unreachable from outside Tailscale, SSH key-only login, backups.
