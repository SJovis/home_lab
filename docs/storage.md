# Storage and Backups

**Last updated:** 2026-11-01
**Host:** `pve01` (Proxmox VE 9.1.1, bare metal)

This page covers disks, Proxmox storage pools, backup destinations, and restore testing. For the overview see [README.md](../README.md). For the working log see [status.md](../status.md).

## Disks

| Device | Type | Size | Role | State |
| -------- | ------ | ------ | ------ | ------- |
| `nvme0n1` | NVMe SSD | 256 GB | Active Proxmox install and VM disks | In use |
| `sda` | SATA SSD | 256 GB | Unused. Wiped 2026-11-01; planned backup target | Empty, no partitions |
| External drive | HDD, USB | 1 TB (about 85 GB used) | Personal files only | Disconnected until a plan exists |

SMART health (checked 2026-11-01, `smartctl`; keep serial numbers out of this repo):

| Disk | Overall | Notes |
| ------ | --------- | ------- |
| `nvme0n1` | PASSED | No critical warning, 0 media errors, 100% spare, 29% of rated life used, about 30,700 power-on hours, 47 C |
| `sda` | PASSED | 2 reallocated sectors (small, but not zero), about 480 power-on hours reported, 21 C |

The NVMe is about a third of the way through its rated life, so it is fine for now but not unlimited. Re-check `sda`'s reallocated sector count monthly. If it rises, treat the disk as failing and do not use it as the only copy of anything.

## Proxmox storage

Defined in `/etc/pve/storage.cfg`. No external or network storage is configured.

| Storage ID | Type | Backing | Content | Notes |
| ------------ | ------ | --------- | --------- | ------- |
| `local` | Directory | `/var/lib/vz` on the root LV (~69 GB) | ISO, templates, backups, import | Same disk as the VMs |
| `local-lvm` | LVM-thin | Pool `data` in VG `pve` (~141 GB) | VM disks, container volumes | Active VM storage |

### Layout on `nvme0n1` (VG `pve`, ~237 GB)

| Logical volume | Size | Purpose |
| ---------------- | ------ | --------- |
| `swap` | 8 GB | Swap |
| `root` | ~69 GB | Proxmox OS and the `local` directory storage |
| `data` (thin pool) | ~141 GB | VM disks |
| (unallocated) | ~16 GB | Free in the volume group |

### Current VM disks

| VMID | Name | Disks | Provisioned |
| ------ | ------ | ------- | ------------- |
| 1004 | nexuslab | EFI disk (4 MB) and a 160 GB data disk | 160 GB |

### Thin pool overcommit

VM `1004`'s 160 GB disk is larger than the ~141 GB pool it sits in. Thin provisioning allows this, and the pool was 6.5% used on 2026-11-01. If the guest ever writes more than the pool can hold, the pool fills, and a full thin pool can corrupt every volume in it.

- Check usage regularly: `lvs pve` (watch `Data%` and `Meta%` on `data`).
- Keep Nexus blob storage within a size that fits the pool, or plan to grow the pool using the ~16 GB free in the VG.
- Do not create additional large VM disks without recalculating.

## Former install on `sda`

The SATA SSD held an earlier Proxmox install, which Proxmox had renamed `pve-OLD-*` because another `pve` volume group already existed. It was reviewed read-only on 2026-11-01:

- two template base volumes and about 14 VM and container disks, mostly linked clones of the templates
- no backup archives and nothing stored under `/root`
- all of it was old test and lab leftovers

**Decision (2026-11-01):** wiped. The volume group and partition table were removed and the SSD was trimmed with `blkdiscard`. Verified afterwards: `lsblk` shows no partitions on `sda`, and `vgs` lists only `pve`.

## Backups

**Current state:** no scheduled backups. One manual backup of `1004 nexuslab` was taken on 2026-11-01 to `local`, which is on the same disk as the VM. It protects against a bad change, not against disk failure.

| Item | Status |
| ------ | -------- |
| Scheduled backup job | None |
| Backup destination on a separate disk | Not set up |
| Offsite or offline copy | None |
| Last successful restore test | None yet |
| NAS | Not available (no budget right now) |

### Options without a NAS

Neither option protects against fire, theft, or the whole host dying.

| Option | Protects against | Cost | Notes |
| -------- | ------------------ | ------ | ------- |
| `sda` SATA SSD (cleared) | Failure of the NVMe disk | None | Separate hardware, always attached. Roughly 237 GB. Has 2 reallocated sectors, so do not make it the only copy. |
| 1 TB external drive | NVMe failure, plus offline when unplugged | None | Large and removable, but it also holds personal files. Keep backups on a separate partition or folder. |
| Both | Either single disk failing, plus a removable second copy | None | Closest to a 2-copy setup without new hardware. |

**Proposed plan (not decided):**

1. Partition and format `sda`, then add it as a Proxmox directory storage for backups.
2. Schedule a nightly or weekly `vzdump` of `1004` to that storage, with a short retention (for example keep the last 3).
3. Periodically copy the newest archive to the external drive and disconnect it.
4. Run a restore test (below) after the first successful backup, then after any major change.

Backup archives (`*.vma.zst` and similar) are blocked by `.gitignore`. Never commit them.

### Manual backup (reference)

```
df -h /var/lib/vz
vzdump 1004 --storage <backup-storage> --mode snapshot --compress zstd
```

Check free space first. The root volume is only about 69 GB.

## Restore test

A backup that has never been restored is untested. Do this after the first backup is on a separate disk.

1. Pick an unused VMID (for example `9001`) so the real VM is untouched.
2. Restore: `qmrestore <archive> 9001 --storage local-lvm --unique 1`. The `--unique` flag gives the copy a new MAC address.
3. Before starting it, set the network device to disconnected (VM → Hardware → Network Device → tick Disconnect) so it cannot clash with `1004` on the LAN.
4. Start the copy. Confirm it boots and Nexus starts.
5. Shut down and delete the test VM: `qm destroy 9001 --purge`.
6. Record the date and result in the table below and in [status.md](../status.md).

| Date | Archive | Result | Notes |
| ------ | --------- | -------- | ------- |
| [none yet] | | | |

## Open questions

- Does `sda`'s reallocated sector count (2 on 2026-11-01) stay stable?
- What is the long-term role of the 1 TB external drive (personal files only, or also a backup copy)?
- What schedule and retention fit the free space on the backup target?
- Where will the planned surveillance VM store recordings? `local-lvm` is overcommitted and `sda` is earmarked for backups, so this needs its own disk or a decision on sharing. See [network.md](network.md).

## Related

- [README.md](../README.md): Storage and Backups, Guest Inventory
- [status.md](../status.md): checklists, decisions, session notes
