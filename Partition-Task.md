# Task 1: Create a New Partition of 5GB, Format It with ext4, and Mount It Persistently

This document outlines the steps to create a new partition of 5GB, format it with the `ext4` file system, and mount it persistently on a Linux system.

---

## **Step 1: Identify Available Disks**

Use the following commands to identify available disks and ensure there is sufficient free space:
```bash
lsblk
fdisk -l
```
Check the output for an unpartitioned disk (e.g., `/dev/sdb`).

---

## **Step 2: Create a 5GB Partition**

1. Open the disk with `fdisk`:
   ```bash
   fdisk /dev/sdb
   ```
2. Follow the prompts:
   - Press `n` to create a new partition.
   - Choose `t` to change the partition type (optional, if required).
   - Select the partition type code, e.g., `83` for Linux.
   - Press `p` to view the partition table.
   - Specify the starting sector (press Enter for default).
   - Specify the ending sector (e.g., `+5G` for a 5GB partition).
   - Press `w` to write the changes and exit.

3. Inform the kernel of partition changes:
   ```bash
   partprobe /dev/sdb
   fdisk -fp
   ```

4. Verify the partition:
   ```bash
   lsblk
   ```
   You should see the newly created partition (e.g., `/dev/sdb1`).

---

## **Step 3: Format the Partition**

Format the new partition with the `ext4` file system:
```bash
mkfs.ext4 /dev/sdb1
```

---

## **Step 4: Create a Mount Point**

Create a directory where the partition will be mounted:
```bash
mkdir /mnt/newpartition
```

---

## **Step 5: Mount the Partition Temporarily**

Mount the partition to the directory:
```bash
mount /dev/sdb1 /mnt/newpartition
```

Verify the mount:
```bash
df -h
```

---

## **Step 6: Configure Persistent Mount**

To make the mount persistent across reboots, add an entry to `/etc/fstab`:

1. Open `/etc/fstab` in a text editor:
   ```bash
   nano /etc/fstab
   ```

2. Add the following line:
   ```plaintext
   /dev/sdb1   /mnt/newpartition   ext4   defaults   0 0
   ```

3. Save and exit the editor.

4. Test the configuration:
   ```bash
   umount /mnt/newpartition
   mount -a
   ```

5. Verify the mount:
   ```bash
df -h
```

---

## **Step 7: Validation**

1. Check the partition size and usage:
   ```bash
   lsblk
   ```
2. Verify the file system:
   ```bash
   file -s /dev/sdb1
   ```

---

## **Summary**

You have successfully created a 5GB partition, formatted it with `ext4`, and mounted it persistently. The partition will remain mounted even after a reboot.
