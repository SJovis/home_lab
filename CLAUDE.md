# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Documentation for a personal homelab: a single Proxmox VE 9.x host (HP EliteDesk, hostname `pve01`) currently running a Sonatype Nexus VM on a flat home network (the game server and Teamspeak VMs were deleted on 2026-11-01). It also serves as a DevOps bootcamp practice log. There is no application code, build, lint, or test tooling yet; the work is writing and maintaining Markdown docs and sanitized config files.

## Key files and how they relate

- `README.md`: the public-facing overview (topology, hardware, software, guest inventory, storage, security notes, changelog, roadmap, repository layout). Contains `[bracketed placeholders]` still waiting for real values (CPU/RAM/storage, Nexus version, guest OS). Don't invent values for them; ask the user.
- `status.md`: the working log. Holds current environment, Done / In Progress / Next checklists, Open Questions, a dated Decisions Log, and dated Session Notes. Update **Last updated**, the checklists, and add a Session Notes entry when a session changes the state of the lab or the docs.
- `docs/`: topic deep-dives. The README's layout section plans `network.md`, `storage.md`, and `guests.md`; none exist yet.
- `configs/`: sanitized configuration files only (currently empty).
- `lessons-learned.md` and `roadmap.md` are planned at the repo root but don't exist yet. The roadmap currently lives in the README and the "Next" section of `status.md`.

Facts are duplicated across `README.md` and `status.md` (guest inventory, network, storage, goals). When one changes, update the other so they stay consistent. When a release is cut, bump the version in both and add a README Changelog entry.

## Conventions

- The repo is public. Never commit real public IPs, credentials, hostnames beyond what's already documented, or host-specific secrets. Network addresses such as `192.168.1.0/24` are explicit placeholders. `.gitignore` already blocks env/key/pem files, `secrets/`, Terraform state/tfvars, and Proxmox backup dumps (`*.vma.zst`, etc.). Pre-commit secret scanning runs gitleaks via the tracked `.githooks/pre-commit`; after cloning, enable it with `git config core.hooksPath .githooks` (requires `gitleaks` in PATH).
- Strip secrets and identifying details from any config before it goes into `configs/`.
- Use absolute dates (YYYY-MM-DD) in the Decisions Log and Session Notes.
- Guests are identified by Proxmox VMID plus name (e.g. `1004 nexuslab`); keep that format in the inventory tables.
- Versioning: v0.1 is the flat-network baseline; VLAN segmentation (management, guest, IoT) is planned for v0.2.
- Flag any change that affects exposure (port forwarding, remote access, firewall rules). State the risk and recommend WireGuard over port forwarding.
- Never invent specs, versions, or addresses. If a value is unknown, leave the placeholder and ask the user.
- Before finishing a session that changes docs, update status.md (Last updated, checklists, Session Notes).
