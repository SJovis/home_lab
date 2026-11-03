# Network

**Last updated:** 2026-11-03
**Version:** v0.1 (flat network). Segmentation is planned for v0.2.

This page covers the physical layout, the current and target topology, and the surveillance camera plan. For the overview see [README.md](../README.md). For the working log see [status.md](../status.md). Addresses are placeholders (`192.168.1.0/24`).

## Physical layout (by floor)

| Floor | Equipment | Connection |
| ------- | ----------- | ------------ |
| 2nd floor | Switch (unmanaged) | Wired uplink to the router |
| 2nd floor | `pve01` (HP EliteDesk, Proxmox VE) | Wired to the switch |
| 2nd floor | Gaming / work PC | Wired to the switch |
| 1st floor | ISP router | Gateway, DHCP, Wi-Fi; wired to the 2nd-floor switch |
| 1st floor | TV box, feeding the TV | Wi-Fi |
| Ground floor (R/C) | Nothing yet | n/a |

## Current topology (v0.1)

```
Internet (fibre)
  |
[ISP router, 1st floor]  NAT, DHCP, Wi-Fi
  |  \
  |   `-- Wi-Fi: smartphone, TV box (-> TV, 1st floor)
  |
[Switch, 2nd floor]
  |-- Gaming / work PC
  `-- pve01 (Proxmox VE)
        `-- 1004 nexuslab (Nexus)
```

Single flat network. Remote access to the host is through Tailscale. No port forwarding is active.

**Known limitation:** all devices share one network, so a compromised phone, TV box or camera could reach `pve01` and its guests.

## Target: surveillance cameras (planned, not built)

Goal: two PoE cameras on a new PoE switch connected to the router, with the surveillance system running in a VM on `pve01`.

```
[ISP router, 1st floor]
  |-- [Switch, 2nd floor] -- PC, pve01 (-> surveillance VM)
  `-- [PoE switch, new]
        |-- PoE camera 1
        `-- PoE camera 2
```

| Item | Status |
| ------ | -------- |
| PoE switch | Not purchased, model `[TBD]` |
| 2 x PoE cameras | Not purchased, models `[TBD]` |
| Surveillance VM | Not created. VMID `[TBD]`, software `[TBD]` |
| Camera placement and cable runs | `[TBD]` |

### Points to settle before building

- **Isolation:** on the flat network the cameras could reach `pve01` and the other guests, and a compromised camera is a real risk. Put them in a camera or IoT VLAN that can reach only the surveillance VM, with no internet access. This needs a managed PoE switch and a router (or other device) that supports VLANs. Router VLAN support is unconfirmed.
- **Exposure:** do not port-forward camera or surveillance VM access. For viewing away from home use Tailscale or WireGuard.
- **Recording storage:** continuous video needs its own capacity. `local-lvm` is ~141 GB and already overcommitted, and the SATA SSD is the planned backup target. Pick a disk for recordings and decide retention. See [storage.md](storage.md).
- **VM resources:** the host has 4 cores and 15.5 GiB RAM shared with Nexus. Check the chosen software against that, and whether the CPU can decode the streams.
- **PoE switch size:** 2 cameras plus the uplink, with spare ports and enough PoE budget for the chosen cameras.

## VLAN plan (v0.2, not decided)

| Segment | Purpose | VLAN ID | Subnet |
| --------- | --------- | --------- | -------- |
| Management | Proxmox, switches, router admin | `[TBD]` | `[TBD]` |
| Guest | Trusted lab VMs | `[TBD]` | `[TBD]` |
| IoT | TV box, smart devices | `[TBD]` | `[TBD]` |
| Cameras | PoE cameras only | `[TBD]` | `[TBD]` |

## Open questions

- Does the ISP router support VLANs, or is a dedicated router plus a managed switch needed?
- Which PoE switch fits (managed, ports, PoE budget)?
- Where do recordings live, and how long are they kept?
- Which surveillance software will run in the VM?

## Related

- [README.md](../README.md): Current Topology, Hardware, Roadmap
- [storage.md](storage.md): disks and backups
- [status.md](../status.md): checklists, decisions, session notes
