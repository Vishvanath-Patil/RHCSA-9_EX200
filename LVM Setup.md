# Task 2: Set Up an LVM with 20GB Disk Space, Create a 10GB Logical Volume, and Expand It by 5GB

This document provides step-by-step instructions to configure a Logical Volume Manager (LVM) setup, create a 10GB Logical Volume (LV), and expand it by an additional 5GB.

---

## **Step 1: Identify Available Disks**

Identify available unpartitioned disks to use for the LVM setup:
```bash
lsblk
fdisk -l
```
Check the output for an unpartitioned disk (e.g., `/dev/sdc`).

---

## **Step 2: Create a Partition for LVM**

1. Open the disk with `fdisk`:
   ```bash
   fdisk /dev/sdc
   ```
2. Follow the prompts:
   - Press `n` to create a new partition.
   - Choose `p` for a primary partition.
   - Accept the default partition number.
   - Specify the starting sector (press Enter for default).
   - Specify the ending sector (e.g., `+20G` for 20GB partition).
   - Press `t` to change the partition type.
   - Enter `8e` for the LVM partition type.
   - Press `w` to write the changes and exit.

3. Inform the kernel of partition changes:
   ```bash
   partprobe /dev/sdc
   ```

4. Verify the partition:
   ```bash
   lsblk
   ```
   You should see the new partition (e.g., `/dev/sdc1`).

---

## **Step 3: Create a Physical Volume (PV)**

Convert the partition to a physical volume:
```bash
pvcreate /dev/sdc1
```
Verify the PV creation:
```bash
pvs
```

---

## **Step 4: Create a Volume Group (VG)**

Create a volume group named `vgdata` using the physical volume:
```bash
vgcreate vgdata /dev/sdc1
```
Verify the VG creation:
```bash
vgs
```

---

## **Step 5: Create a Logical Volume (LV)**

Create a 10GB logical volume named `lvdata` in the volume group `vgdata`:
```bash
lvcreate -L 10G -n lvdata vgdata
```
Verify the LV creation:
```bash
lvs
```

---

## **Step 6: Format and Mount the Logical Volume**

1. Format the logical volume with the `ext4` file system:
   ```bash
   mkfs.ext4 /dev/vgdata/lvdata
   ```

2. Create a mount point:
   ```bash
   mkdir /mnt/lvdata
   ```

3. Mount the logical volume:
   ```bash
   mount /dev/vgdata/lvdata /mnt/lvdata
   ```

4. Verify the mount:
   ```bash
   df -h
   ```

---

## **Step 7: Expand the Logical Volume**

1. Extend the logical volume by 5GB:
   ```bash
   lvextend -L +5G /dev/vgdata/lvdata
   ```

2. Resize the file system to use the extended space:
   ```bash
   resize2fs /dev/vgdata/lvdata
   ```

3. Verify the resized LV and file system:
   ```bash
   lvs
   df -h
   ```

---

## **Step 8: Configure Persistent Mount**

To ensure the LV is mounted persistently after a reboot, add an entry to `/etc/fstab`:

1. Open `/etc/fstab` in a text editor:
   ```bash
   nano /etc/fstab
   ```

2. Add the following line:
   ```plaintext
   /dev/vgdata/lvdata   /mnt/lvdata   ext4   defaults   0 0
   ```

3. Save and exit the editor.

4. Test the configuration:
   ```bash
   umount /mnt/lvdata
   mount -a
   ```

5. Verify the mount:
   ```bash
   df -h
   ```

---

## **Summary**

You have successfully set up an LVM with 20GB disk space, created a 10GB logical volume, formatted and mounted it, and expanded the LV by 5GB. The logical volume is now ready for use and persistently mounted.
---
```bash
Check Available Free Space: Before running the lvextend command, you should verify how much free space is available in your volume group. You can use the following command to check:

vgdisplay
Look for the "Free PE" (Physical Extents) value to determine how much free space is available.

Extend the Logical Volume: If you are sure there is free space, you can run:

lvextend -l +100%FREE /dev/mapper/rhel-root
Resize the Filesystem: After extending the logical volume, you need to resize the filesystem to use the new space. Depending on the filesystem type, you can run one of these commands:

For ext4:

resize2fs /dev/mapper/rhel-root
For XFS (common in newer distributions):

xfs_growfs /dev/mapper/rhel-root
Verify the Changes: After completing the steps, you can check the new size of the logical volume and filesystem:

lvdisplay /dev/mapper/rhel-root
df -h
This will confirm that the logical volume has been extended and that the filesystem is now utilizing the additional space.
```
