```bash
fdisk /dev/vdb
command (m for help) : n
+1G
t 
8E
w
partprobe
partprobe /dev/vdb
pvcreate /dev/vdb1
vgcreate myvg /dev/vdb1
lvcreate -n mylv -l 40 myvg /dev/vdb1
mkfs.ext4 /dev/myvg/mylv
mkdir /mylav
blkid
Copy --> /dev/mapper/myvg-mylv
vim /etc/fstab
/dev/mapper/myvg-mylv /mylv ext4 defaults 0 0
wq
mount -av
lsblk

Task
#lvdispaly
Copy --> LV Path  /dev/myvg/mylv
lvextend -L 260M /dev/myvg/mylv
lsblk
df -Th
resize2fs /dev/myvg/mylv
df -Th

Create 512MB SWAP Partition

fdisk /dev/vdb
n --> new
Last sector +512M
t
82
w
partprobe /dev/vdb
mkswap /dev/vdb2
UUID= swap swap defaults 0 0
mount -a
swapon /dev/vdb2


swapon -a

Create a logical volume of name "wshare" from a volume group name "wgroup" physical extends of 16M and logical volume should have size of 50extents. Formate with vfat filesystem and permanently mounted to /mnt/wshare
lvname = wshare
vgname = wgroup
physical extend 16M
lv lenght 50 extend
fdisk /dev/vdb
F
n
p
+1G
t
8E
w
partprobe /dev/vdb
pvcreate /dev/vdb4
vgcreate -s 16M wgroup /dev/vdb4
lvcreate -n wshare -l 50 wgroup /dev/vdb1
lvdisplay
copy --> LV Path : /dev/wgroup/wshare
mkfs.vfat /dev/wgroup/wshare
mkdir /mnt/wshare
blkid 
Copy --> /dev/mapper/wgroup-wshare
vim /etc/fstab
/dev/mapper/wgroup-wshare /mnt/wshare vfat defaults 0 0

Q1. give sudo privillage to user or group
vim /etc/sudoers
add below
#Allow root to run any commands anywhere
root ALL=(ALL) ALL
username ALL=(ALL) ALL
username ALL=(ALL) NOPASSWD: ALL  ---> Without password

For Group
## Allows people in group wheel to run all commnads
%wheel ALL=(ALL) ALL
%groupname ALL=(ALL) NOPASSWD: ALL

Q2.Set the default max and min days for the password policy.

vim /etc/login.defs
PASS_MAX_DAYS 90
PASS_MIN_DAYS 30
PASS_WARN_AGE 5

Q3. Set welcome note for the server.
vim /etc/profile
echo "WELCOME TO SERVERA"
source WELCOME TO SERVERA

Q4. Question on File Permission and umask
dr-xr-xr-x
-r--r--r--

Full Permission      777          666
Default              555          444
                     222          222
		
useradd mohit
su - mohit
vim .bashrc
umask 222
source .bashrc	
umask 222
touch f1
mkdir d1 
ls -l

Q5. Script file
- create a script file with the name 'script.sh' inside the '/bin' and locate all files from '/usr/local' directory which has more than 30k and lessthan 50k copy under '/root/d1'.
-set the sgid permission for this directory.

mkdir -p /mnt/freespace
touch /mnt/freespace/search.txt
vim /bin/test.sh

#!/bin/bash

find /usr/shre -size +30k -size -50k > /mnt/freespace/serach.txt
chmod g+s /mnt/freespace

chmod +x test.sh
```
