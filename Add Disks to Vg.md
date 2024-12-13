# Task 5: Configure a Scenario Where Multiple Disks Are Added to a Volume Group

This guide explains how to add multiple disks to a Volume Group (VG) for Logical Volume Manager (LVM) configuration.

---

## **Step 1: Identify Available Disks**

Use the following commands to check for available unpartitioned disks:
```bash
lsblk
fdisk -l
```
Identify the disks to be added (e.g., `/dev/sdb`, `/dev/sdc`).

---

## **Step 2: Create Partitions for LVM**

1. For each disk, create an LVM-compatible partition:
   ```bash
   fdisk /dev/sdb
   ```
2. Follow the prompts:
   - Press `n` to create a new partition.
   - Press `p` for a primary partition.
   - Accept the default partition number.
   - Specify the starting sector (press Enter for default).
   - Specify the ending sector (use the entire disk or required size).
   - Press `t` to change the partition type.
   - Enter `8e` for LVM.
   - Press `w` to write the changes and exit.

Repeat the above steps for additional disks (e.g., `/dev/sdc`).

3. Inform the kernel of partition changes:
   ```bash
   partprobe /dev/sdb
   partprobe /dev/sdc
   ```

4. Verify the partitions:
   ```bash
   lsblk
   ```

---

## **Step 3: Create Physical Volumes (PVs)**

Convert the partitions into physical volumes:
```bash
pvcreate /dev/sdb1 /dev/sdc1
```
Verify the PV creation:
```bash
pvs
```

---

## **Step 4: Create or Extend the Volume Group (VG)**

1. If creating a new VG, run:
   ```bash
   vgcreate vgdata /dev/sdb1 /dev/sdc1
   ```
   Replace `vgdata` with your desired VG name.

2. If extending an existing VG:
   ```bash
   vgextend vgdata /dev/sdb1 /dev/sdc1
   ```

3. Verify the VG:
   ```bash
   vgs
   ```

---

## **Step 5: Create or Extend a Logical Volume (LV)**

1. Create a new LV using the extended VG:
   ```bash
   lvcreate -L 10G -n lvdata vgdata
   ```
   Replace `10G` with the desired size and `lvdata` with the logical volume name.

2. If extending an existing LV:
   ```bash
   lvextend -L +10G /dev/vgdata/lvdata
   ```
   Replace `+10G` with the additional size.

3. Resize the file system to utilize the extended LV (if applicable):
   ```bash
   resize2fs /dev/vgdata/lvdata
   ```

4. Verify the LV:
   ```bash
   lvs
   ```

---

## **Step 6: Format and Mount the Logical Volume**

1. Format the logical volume:
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

## **Step 7: Configure Persistent Mount**

To make the mount persistent, add an entry to `/etc/fstab`:

1. Open `/etc/fstab`:
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

You have successfully configured a scenario where multiple disks are added to a Volume Group. This setup allows flexible and scalable disk management for your logical volumes.
