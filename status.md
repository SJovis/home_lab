# Status

**Last updated:** 2026-11-01
**Current version:** v0.1 (baseline)

## Environment

- Host: HP EliteDesk running Proxmox VE 9.1.1 (hostname: pve01)
- Network: single flat network behind ISP router (192.168.1.0/24 placeholder), wired and Wi-Fi clients
- Guests (all Ubuntu 26.04.1 LTS):
  - 1004 `nexuslab`: Sonatype Nexus artifact repository (running)
  - Palworld, Teamspeak and test VMs deleted 2026-11-01
- Storage: NVMe SSD holds the active Proxmox install (LVM-thin `local-lvm` ~141 GB pool, `local` dir on the ~69 GB root LV). SATA SSD is empty (old test install wiped 2026-11-01) and is the planned backup target. The 1 TB external drive is personal files only and disconnected until a plan exists
- Backups: no scheduled backups. One manual backup of 1004 on `local` (same disk as the VM). The external drive holds personal files only. No budget for a NAS right now
- Known limitation: all devices share one network, so a compromised IoT or mobile device can reach the Proxmox host and guests

## Goals (current)

- Run a reliable home environment, rebuilt from scratch with better planning
- Game servers for friends: low priority, revisit only if a game comes up that changes the plans
- Practice DevOps and systems administration on real infrastructure
- Document design decisions, mistakes, and changes over time

## Done

- [x] Proxmox VE installed on EliteDesk
- [x] Sonatype Nexus running in a VM
- [x] Game server and Teamspeak VMs created
- [x] v0.1 README drafted and updated with current topology and guests
- [x] README placeholders filled in (hardware, Nexus 3.97.0-13, Ubuntu 26.04.1 LTS guests, Proxmox 9.1.1)
- [x] 2FA enabled on Proxmox (TOTP plus recovery keys), 2026-11-01
- [x] T2: Reviewed the old install on the SATA SSD (test leftovers, no backups) and wiped the disk, 2026-11-01
- [x] T1: SMART check on both internal disks, both PASSED, 2026-11-01 (SATA SSD has 2 reallocated sectors, re-check monthly)
- [x] Drafted `docs/storage.md` (disks, pools, backup options, restore-test checklist), 2026-11-01
- [x] Deleted unused VMs 1001, 1002, 1003, 1005; kept 1004 nexuslab (2026-11-01)
- [x] Port forwarding rules for game server and Teamspeak removed (2026-11-01)
- [x] Pre-commit secret scanning (gitleaks) via tracked `.githooks/pre-commit`

## In Progress

- [ ] T3: Set up a scheduled backup of `1004 nexuslab` to a separate disk, then run a restore test (T1 and T2 are done)
- [ ] T4: Confirm Proxmox web UI and SSH are not reachable from outside Tailscale

## Next

- [ ] Watch thin pool headroom on `local-lvm`: pool is 6.5% used, but VM 1004's 160 GB disk is larger than the ~141 GB pool
- [ ] Plan VLAN segmentation (management, guest, IoT) and pick VLAN IDs and subnets
- [ ] Plan security camera setup: PoE switch connected to the router
- [ ] Write `docs/network.md` with the current and target topology
- [ ] Start `lessons-learned.md` with the first real issue encountered

## Open Questions

- Router support for VLANs is unconfirmed. Is a dedicated router (VyOS/OPNsense on a small PC) plus a managed switch worth it for segmentation?
- Which PoE switch fits the camera plan, and how many ports are needed?
- Which backup target is acceptable without a NAS: the SATA SSD, the 1 TB external drive, or both? Neither protects against fire, theft or host loss
- Should friends connect via WireGuard instead of port forwarding?

## Decisions Log

- 2026-10-31: Chose flat network for v0.1 baseline; segmentation deferred to v0.2
- 2026-10-31: Public repo with sanitized configs; secrets kept out of git
- 2026-11-01: No separate 2FA on the Tailscale account. Reason: sign-in is already protected by the identity provider's own 2FA, and only devices in the account can reach the tailnet
- 2026-11-01: Deleted the test VMs (teamspeak, TS3, ubuntu26.04, palworld) and start the project from zero with only 1004 nexuslab. Reason: they were small tests and case studies of what the machine could host, never meant to be permanent
- 2026-11-01: Game servers for friends demoted to a low-priority goal, to be planned from the start only if a game changes the plans
- 2026-11-01: Wiped the old Proxmox install on the SATA SSD after a read-only review. Reason: the old VMs and containers were test leftovers, there were no backups on it, and the disk is wanted as the backup target
- 2026-11-01: Pre-commit hook lives in tracked `.githooks/` (enabled per clone with `git config core.hooksPath .githooks`) so it is versioned with the repo

## Session Notes

- 2026-10-31: README updated. Goals revised. Next: fill in hardware specs and plan the VLAN layout.
- 2026-11-01: Added gitleaks pre-commit hook (`.githooks/pre-commit`, scans staged changes). Verified it blocks a fake token and allows clean commits. CLAUDE.md was already committed.
- 2026-11-01: README hardware and Nexus version filled in from the Proxmox summary page. Notes: Tailscale is installed on the host for remote access; Teamspeak was only a trial; game server (Palworld) is idle; no backups yet.
- 2026-11-01: Confirmed Proxmox VE 9.1.1 (docs said 8.x, corrected) and Ubuntu 26.04.1 LTS on all guests. Port forwarding for game server/Teamspeak removed. Game server last hosted Palworld (~6 users).
- 2026-11-01: Proxmox 2FA enabled and tested (TOTP plus recovery keys). Still to do: 2FA on the Tailscale account, confirm Proxmox is unreachable from outside Tailscale, SSH key-only login, backups.
- 2026-11-01: Tailscale 2FA judged unnecessary (provider 2FA, account-only devices). SSH reported working. Backups need a plan: no NAS budget, so options are the external drive or HP internal disk. Next: confirm where VM disks live, then pick a backup target and schedule a restore test.
- 2026-11-01: Disk layout confirmed from `lvs`/`lsblk`/`storage.cfg`: VMs live on internal SSDs, not the external drive (corrected README and status). Found an old Proxmox install on the SATA SSD. Next: decide what to keep from it, confirm real VMIDs, then pick a backup target.
- 2026-11-01: Deleted VMs 1001 teamspeak, 1002 TS3, 1003 ubuntu26.04 and 1005 palworld after a manual backup of 1004. Guest docs now list only 1004 nexuslab. Next: decide what to keep on the old SATA install, then set up a scheduled backup of 1004 to a separate disk.
- 2026-11-01: Wrote `docs/storage.md` and linked it from the README. It holds the disk layout, thin pool overcommit warning, a read-only review procedure for the old SATA install, and a proposed (undecided) backup plan. Next: review the old SATA install, check SMART on both disks, then pick a backup target.
- 2026-11-01: T1 done. Both internal disks pass SMART. NVMe at 29% of rated life; SATA SSD shows 2 reallocated sectors, so recheck monthly and do not rely on it alone. Tasks T1 to T4 now tracked under In Progress. Next: T2, review the old SATA install.
- 2026-11-01: T2 done. Old install on the SATA SSD reviewed read-only (templates, linked clones, containers, no backups), then wiped; verified with lsblk, pvs and vgs. Next: T3, format the SSD and add it as backup storage, schedule a backup of 1004, and run a restore test.
