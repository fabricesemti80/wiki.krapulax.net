# Ceph OSD RocksDB Corruption Recovery

## Issue Summary

**Date:** 2026-01-17  
**Affected Node:** pve-2  
**Affected Component:** OSD.2 (NVMe: `/dev/nvme0n1`)  
**Cluster:** Proxmox Ceph (3-node)

### Symptoms

```bash
$ ceph health
HEALTH_WARN Degraded data redundancy: 74814/224442 objects degraded (33.333%), 113 pgs degraded, 113 pgs undersized

$ ceph osd tree
ID  CLASS  WEIGHT   TYPE NAME       STATUS
 2    ssd  0.46579  osd.2           down
```

### Root Cause

RocksDB corruption in BlueStore's internal database. The SST files became ahead of the WAL (Write-Ahead Log), likely caused by:
- Unexpected power loss
- System crash during write operations
- Storage hardware issues

**Error from journal:**
```
rocksdb: Corruption: SST file is ahead of WALs in CF default
bluestore(/var/lib/ceph/osd/ceph-2) _open_db erroring opening db:
osd.2 0 OSD:init: unable to mount object store
ERROR: osd init failed: (5) Input/output error
```

---

## Recovery Procedure

### Step 1: Diagnose the Issue

```bash
# Check cluster health
ceph health

# Identify which OSD is down
ceph osd tree

# Check OSD logs for errors
journalctl -u ceph-osd@2 --no-pager -n 50
```

### Step 2: Attempt BlueStore Repair (Optional)

Try repair first—if successful, no data loss occurs:

```bash
# Stop the OSD
systemctl stop ceph-osd@2

# Attempt repair
ceph-bluestore-tool repair --path /var/lib/ceph/osd/ceph-2

# If repair fails, try fsck
ceph-bluestore-tool fsck --path /var/lib/ceph/osd/ceph-2

# Restart if repair succeeded
systemctl start ceph-osd@2
```

> **Note:** In this incident, repair failed with the same corruption error.

### Step 3: Destroy and Recreate OSD

When repair fails, the OSD must be destroyed and recreated. Data will be recovered from other replicas.

#### 3.1 Remove OSD from Cluster

```bash
# Mark OSD out and down
ceph osd out 2
ceph osd down 2

# Purge OSD from cluster
ceph osd purge 2 --yes-i-really-mean-it
```

#### 3.2 Clean Up Storage

```bash
# Identify the LVM structure
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT

# Remove LVM (adjust names based on your output)
umount /var/lib/ceph/osd/ceph-2 2>/dev/null
lvremove -f <vg-name>/<lv-name>
vgremove -f <vg-name>
pvremove /dev/nvme0n1

# Wipe the disk completely
ceph-bluestore-tool zap-device --dev /dev/nvme0n1 --yes-i-really-really-mean-it
wipefs -a /dev/nvme0n1
sgdisk --zap-all /dev/nvme0n1
dd if=/dev/zero of=/dev/nvme0n1 bs=1M count=500 conv=fsync
```

#### 3.3 Clean OSD Directory and Reset Systemd

```bash
rm -rf /var/lib/ceph/osd/ceph-2/*
systemctl reset-failed ceph-osd@2
systemctl daemon-reload
```

#### 3.4 Recreate OSD

```bash
# Using Proxmox command
pveceph osd create /dev/nvme0n1

# Alternative: using ceph-volume directly
# ceph-volume lvm create --data /dev/nvme0n1
```

### Step 4: Verify Recovery

```bash
# Check all OSDs are up
ceph osd tree

# Monitor cluster recovery
watch ceph -s
```

Expected output during recovery:
```
health: HEALTH_WARN
        Degraded data redundancy: XX% objects degraded

osd: 3 osds: 3 up, 3 in; XX remapped pgs

recovery: X MiB/s, X objects/s
```

---

## Recovery Timeline

| Time | Action |
|------|--------|
| T+0 | Issue detected, OSD.2 down |
| T+2m | Repair attempt failed |
| T+5m | OSD purged from cluster |
| T+8m | Disk wiped and OSD recreated |
| T+10m | OSD.2 back online, recovery started |
| T+60m (est.) | Full recovery complete, HEALTH_OK |

---

## Prevention

1. **UPS Protection** - Ensure all Ceph nodes have UPS with graceful shutdown
2. **Regular Scrubbing** - Monitor for early signs of corruption
   ```bash
   ceph pg dump | grep -i scrub
   ```
3. **Monitor Disk Health** - Use SMART monitoring
   ```bash
   smartctl -a /dev/nvme0n1
   ```
4. **Ceph Telemetry** - Enable health alerts
   ```bash
   ceph health detail
   ```

---

## Related Commands

```bash
# Check OSD status
ceph osd stat
ceph osd df

# Check PG status
ceph pg stat
ceph pg dump_stuck

# Check recovery progress
ceph -w

# Force recovery priority (use sparingly)
ceph osd set-recovery-max-active 4
```

---

## References

- [Ceph BlueStore](https://docs.ceph.com/en/latest/rados/configuration/bluestore-config-ref/)
- [Proxmox Ceph Documentation](https://pve.proxmox.com/wiki/Deploy_Hyper-Converged_Ceph_Cluster)
- [ceph-bluestore-tool man page](https://docs.ceph.com/en/latest/man/8/ceph-bluestore-tool/)
