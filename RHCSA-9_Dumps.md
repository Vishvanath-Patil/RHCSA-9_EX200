# RHCSA v9 Exam Instructions and Questions

## Important Instructions
- You will be given two VMs:
  - Hostname: **node1.domainX.example.com** (IP: `172.24.10.10`)
  - Hostname: **node2.domainX.example.com** (IP: `172.24.10.11`)
- Total number of questions: **22**
- **Node1**:
  - Root password is already set (no need to reset).
  - Requires **network configuration**.
  - Requires **NTP configuration**.
  - Requires **YUM repository setup**.
- **Node2**:
  - Requires root password recovery.
  - Requires **YUM repository setup**.
  - Does not need network configuration.

- **Firewall and SELinux** are pre-enabled on both systems.

---

## Node1 Questions

### Q1: Configure Network
Set the static parameters:
- IP Address: `172.24.10.10`
- Netmask: `255.255.255.0`
- Gateway: `172.24.10.254`
- Nameserver: `172.24.10.254`
- Domain Name: `domainX.example.com`
- Hostname: `node1.domainX.example.com`

```bash
ip a s
cat /etc/resolv.conf
ip route
nmcli connection show
nmcli connection modify "Wired connection 1" ipv4.address "172.24.10.10/24" \
  ipv4.dns "172.24.10.254" ipv4.gateway "172.24.10.254" ipv4.method manual
nmcli connection reload
hostnamectl set-hostname node1.domainX.example.com
vim /etc/ssh/sshd_config
# 41 PermitRootLogin yes
# 66 PasswordAuthentication yes
:wq
systemctl restart sshd.service
Q2: Configure YUM Repositories
Configure the repositories for BaseOS and AppStream.

bash
Copy code
vim /etc/yum.repos.d/rhel.repo
# [BaseOS]
# name=BaseOS
# baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS/
# gpgcheck=0
# enabled=1

# [AppStream]
# name=AppStream
# baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream/
# gpgcheck=0
# enabled=1
:wq
yum list autofs
Q3: Debug SELinux for a Web Server
Ensure SELinux allows the web server to run on port 82.

bash
Copy code
yum list httpd
systemctl status httpd.service
systemctl enable httpd.service
ls /var/www/html/
cat /etc/httpd/conf/httpd.conf | grep -i "listen"
firewall-cmd --permanent --add-port=82/tcp
firewall-cmd --reload
firewall-cmd --list-all
semanage port -a -t http_port_t -p tcp 82
curl localhost:82/file1
Q4: Create User Accounts
Create a sysadms group and add users natasha, harry, and sarah.

bash
Copy code
groupadd sysadms
useradd -G sysadms natasha
useradd -G sysadms harry
useradd -s /sbin/nologin sarah
echo "trootent" | passwd --stdin harry
echo "trootent" | passwd --stdin natasha
echo "trootent" | passwd --stdin sarah
Q5: Configure Cron Job
Create a cron job for natasha that logs "EX200 in progress" every 2 minutes.

bash
Copy code
systemctl restart crond.service
crontab -eu natasha
# */2 * * * * logger "EX200 in progress"
:wq
systemctl restart crond.service
Q6: Create Collaborative Directory
Create /home/manager directory, set group ownership to sysadms, and set permissions.

bash
Copy code
mkdir /home/sysadms
chgrp sysadms /home/sysadms
chmod 2770 /home/sysadms
Q7: Configure NTP
Sync time with utility.example.com.

bash
Copy code
vim /etc/chrony.conf
# server utility.example.com iburst
:wq
systemctl restart chronyd.service
chronyc sources -v
Q8: Configure AutoFS
Automount remote directories.

bash
Copy code
yum install autofs -y
vim /etc/auto.master
# /rhome/remoteuserX /etc/auto.misc
:wq
vim /etc/auto.misc
# remoteuserX -fstype=rw,nfs,soft,sync utility.example.com:/rhome/remoteuserX
:wq
systemctl restart autofs.service
Q9: Create Container Image
Create a container image named process_files.

bash
Copy code
wget http://utility.example.com/container/Containerfile
podman build -t monitor -f .
podman images
Q10: Create Rootless Container
Create and configure a rootless container named ascii2pdf.

bash
Copy code
podman run -d --name ascii2pdf -v /opt/files:/opt/incoming:Z -v /opt/processed:/opt/outgoing:Z localhost/monitor
podman generate systemd --name ascii2pdf --files --new
node2
Q11: Configure NFS Share
Configure NFS to share the /data directory with client node3 at IP 192.168.1.3.

bash
Copy code
mkdir -p /data
chmod 777 /data
vim /etc/exports
/data 192.168.1.3(rw,sync,no_root_squash)
:wq!
exportfs -a
systemctl start nfs-server
systemctl enable nfs-server
firewall-cmd --permanent --add-service=nfs
firewall-cmd --reload
Q13: Configure Apache Web Server
Install and configure Apache HTTPD service. Ensure the web server serves the default page and is accessible from node3.

bash
Copy code
yum install -y httpd
systemctl start httpd
systemctl enable httpd
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
echo "Welcome to RHCSA Web Server" > /var/www/html/index.html
systemctl status httpd
Q14: Configure SELinux Context for a Directory
Ensure the /data directory on node2 is labeled with the correct SELinux context for a web server directory (httpd_sys_content_t).

bash
Copy code
semanage fcontext -a -t httpd_sys_content_t "/data(/.*)?"
restorecon -Rv /data
ls -Z /data
Q15: Reset Root User Password
Reset the root password to trootent.

bash
Copy code
passwd root
New password: trootent
Retype new password: trootent
Q16: Configure YUM Repos
Configure BaseOS and AppStream YUM repositories.

bash
Copy code
vim /etc/yum.repos.d/rhel.repo
[BaseOS]
name=BaseOS
baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS/
gpgcheck=0
enabled=1

[AppStream]
name=AppStream
baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream/
gpgcheck=0
enabled=1
:wq!
yum list autofs
Q17: Resize a Logical Volume
Resize the logical volume mylv to a size between 290MB and 330MB.

bash
Copy code
lvs
df -hT
lvextend -L 310M /dev/myvg/mylv
resize2fs /dev/mapper/myvg-mylv
lvs
df -hT
Q18: Add a Swap Partition
Add a 512MB swap partition and ensure it is mounted permanently.

bash
Copy code
gdisk /dev/vdb
Command (? for help): n
Partition number (2-128, default 2):
First sector (34-2047, default = 34) or {+-}size{KMGTP}: Press Enter
Last sector (34-2047, default = 2047) or {+-}size{KMGTP}: +512M
Hex code or GUID (L to show codes, Enter = 8300): Press Enter

Command (? for help): t
Partition number (1-2): 2
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): 8200
Changed type of partition to 'Linux swap'

Command (? for help): w
udevadm settle
reboot
lsblk
mkswap /dev/vdb2
vim /etc/fstab
UUID=e5a95dd4-0417-4229-a499-92b29fe9f201 swap swap defaults 0 0
:wq!
mount -a
swapon /dev/vdb2
swapon -s
swapon -d
free -m
Q19: Create a Logical Volume
Create a logical volume named newlv from the volume group newvg.

bash
Copy code
gdisk /dev/vdb
Command (? for help): n
Partition number (3-128, default 3): 3
First sector (34-2047, default = 34) or {+-}size{KMGTP}: Press Enter
Last sector (34-2047, default = 2047) or {+-}size{KMGTP}: +1024M
Hex code or GUID (L to show codes, Enter = 8300): Press Enter

Command (? for help): t
Partition number (1-3): 3
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): 8e00
Changed type of partition to 'Linux lvm'

Command (? for help): w
udevadm settle
pvcreate /dev/vdb3
vgcreate -s 16M newvg /dev/vdb3
vgdisplay
lvcreate -n newlv -l 50 newvg
lvs
mkfs -t ext3 /dev/mapper/newvg-newlv
blkid
mkdir /mnt/newlv
echo "UUID=1902-BFCE /mnt/newlv ext3 defaults 0 0" >> /etc/fstab
mount -a
df -hT
Q20: Configure System Tuning
Choose the recommended tuned profile for the system and set it as the default.

bash
Copy code
yum list tuned
systemctl restart tuned.service
tuned-adm active
Current active profile: balanced
tuned-adm recommend
virtual-guest
tuned-adm profile virtual-guest
tuned-adm active
Current active profile: virtual-guest
