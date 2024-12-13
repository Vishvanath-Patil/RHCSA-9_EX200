## **Disk Management Basics**
### **1. Understanding Partitioning**
- **Partition:** Dividing a disk into separate sections for organizing data.
- Types:
    - Primary: Up to 4 partitions.
    - Extended: Allows more partitions using logical volumes.
### **2. File Systems**
- Common file systems in RHEL: ext4, xfs.
- Default File System: xfs.
  
## **Tasks and Commands**
### **A. Creating Standard Partitions**
**1. View Disk Information:**
```bash
df -Th
lsblk
fdisk -l
```
**Create a Partition:**
```bash
fdisk /dev/sdX
```
- n: Create a new partition.
- p: Primary or e for extended.
- Select sector range, type, and save with w.
**Format the Partition:**
```bash
mkfs.xfs /dev/sdX1
```
- (Replace X1 with your disk name and partition number.)

**Mount the Partition:**
```bash
mkdir /mnt/newdisk
mount /dev/sdX1 /mnt/newdisk
```
Make the Mount Persistent (Edit /etc/fstab):
```bash
/dev/sdX1 /mnt/newdisk xfs defaults 0 0
```
---
## **B. SWAP Partition**
**Create a Partition:**
```bash
fdisk /dev/sdX
```
- n: Create a new partition.
- p: Primary or e for extended.
- t: select file type
- L: List
- p: print for verify
- Select sector range, type, and save with w.
```bash
# Check Existing SWAP:
swapon --show
free -h
# Create SWAP Partition:
mkswap /dev/sdX2
swapon /dev/sdX2

# Add SWAP to /etc/fstab:
/dev/sdX2 swap swap defaults 0 0
```
---
## **C. Logical Volume Management (LVM)**

- LVM: A flexible way to manage disk space, allowing resizing and snapshots.

### Components:
- Physical Volume (PV): Physical disk or partition.
- Volume Group (VG): Collection of PVs.
- Logical Volume (LV): Allocatable units from VG.
### Commands
```bash
# Create Physical Volume:
pvcreate /dev/sdX

# Create Volume Group:
vgcreate vg_name /dev/sdX

# Create Logical Volume:
lvcreate -L 10G -n lv_name vg_name

# Format and Mount LV:
mkfs.xfs /dev/vg_name/lv_name
mkdir /mnt/lv_mount
mount /dev/vg_name/lv_name /mnt/lv_mount

# Resize Logical Volume:
# Increase:
lvextend -L +5G /dev/vg_name/lv_name
xfs_growfs /mnt/lv_mount

# Decrease (requires unmounting):
umount /mnt/lv_mount
lvreduce -L -5G /dev/vg_name/lv_name
mount /dev/vg_name/lv_name /mnt/lv_mount
```
## **Conditions for Creating an LVM Hierarchy**

### 1. Basic Conditions
- a. Physical Storage
    - **Requirement:** At least one physical storage device or partition.
    - Can be an unpartitioned disk (e.g., /dev/sdb) or a partition (e.g., /dev/sdb1).
    - The device should not already contain data unless you're repurposing it.
- b. Disk Label Type
    - The disk must have a valid label (MBR or GPT) created using parted or fdisk.
    - For LVM, the partition type should be marked as 8e (Linux LVM) when using fdisk.
- c. No Existing Mounts
    - Ensure the disk or partition is not already mounted or in use.
- Verify using:
```bash
lsblk
df -h
```
### **2. LVM Component Conditions**
- **a. Physical Volume (PV)**
- The disk or partition must first be initialized as a Physical Volume.
```bash
pvcreate /dev/sdX
```
- **Conditions:**
- The disk must be writable.
- If repurposing a disk, clear existing partitions/data using wipefs:
```bash
wipefs -a /dev/sdX
```
- **b. Volume Group (VG)**
- At least one PV is required to create a Volume Group.
```bash
vgcreate vg_name /dev/sdX
```
- Conditions:
- A VG can consist of one or more PVs.
- The VG size equals the sum of the sizes of all PVs added to it.
**c. Logical Volume (LV)**
- A VG must exist before creating an LV.
```bash
lvcreate -L 10G -n lv_name vg_name
```
- Conditions:
- The requested size (-L) must not exceed the free space in the VG.
- For Thin Provisioning, ensure sufficient space for the thin pool.
### **3. File System and Mounting**
- a. Formatting
After creating an LV, it needs a file system.
```bash
mkfs.xfs /dev/vg_name/lv_name
or
mkfs.ext4 /dev/vg_name/lv_name
```
- b. Mounting
- The LV can then be mounted to a directory:
```bash
mkdir /mnt/myvolume
mount /dev/vg_name/lv_name /mnt/myvolume
```
- 4. Practical Example of LVM Hierarchy Creation
Step-by-Step
1. Create a Physical Volume (PV)
```bash
# 1. Identify the disk: 
lsblk
# 2. Create PV:
pvcreate /dev/sdb
# 3. Create a Volume Group (VG)
# Add PV to VG:
vgcreate my_vg /dev/sdb
# 4. Create a Logical Volume (LV)
# Allocate 10GB for LV:
lvcreate -L 10G -n my_lv my_vg
# 5. Format and Mount LV
# Format the LV:
mkfs.xfs /dev/my_vg/my_lv
# Mount it:
mkdir /mnt/mydata
mount /dev/my_vg/my_lv /mnt/mydata
```
## **LVM Hierarchy Summary**
```bash
Physical Volume (PV):
Base level representing raw disks or partitions.
Command: pvcreate.

Volume Group (VG):
Groups multiple PVs into a single storage pool.
Command: vgcreate.

Logical Volume (LV):
Allocates space from a VG for use as a file system or SWAP.
Command: lvcreate.

Conditions for Advanced Operations

a. Extending an LV
Conditions:
Ensure the VG has free space.
Command:
# lvextend -L +5G /dev/vg_name/lv_name
# xfs_growfs /mnt/mountpoint

b. Reducing an LV
Conditions:
Unmount the LV before resizing.
Backup data as reducing can lead to data loss.
Command:
umount /mnt/mountpoint
# lvreduce -L -5G /dev/vg_name/lv_name
# mount /dev/vg_name/lv_name /mnt/mountpoint
```
