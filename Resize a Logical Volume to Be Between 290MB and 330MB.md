# Task: Resize a Logical Volume to Be Between 290MB and 330MB

This guide explains how to resize an existing logical volume named `mylv` to a size within the range of 290MB to 330MB, ensuring persistence after a reboot.

---

## **Prerequisites**

- Ensure you have sufficient free space in the volume group (`myvg`).
- Verify the current size of the logical volume (`mylv`).

---

## **Step 1: Verify the Current Logical Volume and File System Size**

1. Check the details of the logical volume:
   ```bash
   lvs
   ```
2. Verify the file system type and usage:
   ```bash
   df -hT
   ```

---

## **Step 2: Resize the Logical Volume**

1. Resize the logical volume to 310MB:
   ```bash
   lvextend -L 310M /dev/myvg/mylv
   ```
   This command increases the size of `mylv` to 310MB.

2. Confirm the resizing:
   ```bash
   lvs
   ```

---

## **Step 3: Resize the File System**

1. Adjust the file system to utilize the new size:
   ```bash
   resize2fs /dev/mapper/myvg-mylv
   ```
   This ensures the file system uses the entire logical volume.

2. Verify the file system size:
   ```bash
   df -hT
   ```

---

## **Step 4: Verify the Configuration Post-Reboot**

To ensure the logical volume persists after a reboot:

1. Verify the entry in `/etc/fstab`:
   ```bash
   nano /etc/fstab
   ```
   Ensure the logical volume is correctly listed for automatic mounting.

2. Reboot the system:
   ```bash
   reboot
   ```

3. After rebooting, confirm the size and mount status:
   ```bash
   lvs
   df -hT
   ```

---

## **Commands Summary**

```bash
# Check logical volume and file system details
lvs
df -hT

# Extend the logical volume
lvextend -L 310M /dev/myvg/mylv

# Resize the file system
resize2fs /dev/mapper/myvg-mylv

# Verify the changes
lvs
df -hT

# Verify after reboot
reboot
lvs
df -hT
```

---

## **Summary**

You have successfully resized the logical volume `mylv` to 310MB, ensuring that it is between 290MB and 330MB and persists after a reboot.
