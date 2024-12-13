# Add a 512MB Swap Partition and Mount it Permanently Using fdisk

Follow the steps below to add a swap partition of 512MB to your system using `fdisk` and mount it permanently.

## 1. Create the Swap Partition

```bash
# fdisk /dev/vdb
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Command (m for help): p
Partition number (1-4, default 1): 2
First sector (2048-2047, default = 2048) or {+-}size{KMGTP}: Press Enter
Last sector, +sectors or +size{KMGTP}: +512M

Command (m for help): t
Partition number (1-4): 2
Hex code or GUID (L to show codes, Enter = 8300): 82

Command (m for help): w
```
This creates the swap partition and writes the changes.

## 2. Reboot the System

```bash
# udevadm settle
# reboot
```

## 3. Set Up the Swap Partition

After rebooting, run the following commands:

```bash
# lsblk
# mkswap /dev/vdb2
```

Copy the UUID for the swap partition (e.g., `e5a95dd4-0417-4229-a499-92b29fe9f201`), which you can get using:

```bash
# blkid /dev/vdb2
```

## 4. Edit `/etc/fstab`

Edit the `/etc/fstab` file to make the swap partition permanent by adding the UUID:

```bash
# vim /etc/fstab
UUID=e5a95dd4-0417-4229-a499-92b29fe9f201 swap swap defaults 0 0
:wq!
```

## 5. Mount the Swap Partition

Run the following commands to mount the swap partition and activate it:

```bash
# mount -a
# swapon /dev/vdb2
```

## 6. Verify the Swap Partition

Check the swap status with:

```bash
# swapon -s
# free -m
```

This will confirm that the swap partition is active and the system is using it.
