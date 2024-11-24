RHCSA v9 Exam Questions
Important Instructions
You will be given two VMs:
node1.domainX.example.com (IP: 172.24.10.10)
node2.domainX.example.com (IP: 172.24.10.11)
Total number of questions: Around 22.
For one system, the root password is already set (no need to reset), but for the second system, the root password needs to be recovered.
In one system, network configuration is required, while the other one already has it configured.
NTP needs to be configured on only one system (not both).
YUM repositories need to be configured on both systems.
Firewall and SELinux are both pre-enabled.
node1
Q1. Configure Network and Set Static Parameters
Consider the machine is configured as DHCP, and you need to configure it with static parameters.

Parameters:

IP Address: 172.24.10.10
Netmask: 255.255.255.0
Gateway: 172.24.10.254
Nameserver: 172.24.10.254
Domain Name: domainX.example.com
Hostname: node1.domainX.example.com
Steps:

bash
Copy code
# ip a s
# cat /etc/resolv.conf
# ip route
# nmcli connection show
# nmcli connection modify "Wired connection 1" ipv4.address "172.24.10.10/24" ipv4.dns "172.24.10.254" ipv4.gateway "172.24.10.254" ipv4.method manual
# nmcli connection reload
# hostname
# hostnamectl set-hostname node1.domainX.example.com
# vim /etc/ssh/sshd_config
# PermitRootLogin yes
# PasswordAuthentication yes
:wq!
# systemctl restart sshd.service
Make SSH connection from the exam-host to node1.domainX.example.com and continue.

Q2. Configure YUM Repos
Configure two YUM repositories: BaseOS and AppStream.

BaseOS:

bash
Copy code
http://content.example.com/rhel8.0/x86_64/dvd/BaseOS
AppStream:

bash
Copy code
http://content.example.com/rhel8.0/x86_64/dvd/AppStream
Steps:

bash
Copy code
# vim /etc/yum.repos.d/rhel.repo
[BaseOs]
name=BaseOs
baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS/
gpgcheck=0
enabled=1

[AppStream]
name=AppStream
baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream/
gpgcheck=0
enabled=1
:wq
# yum list autofs
Q3. Debug SELinux for Web Server on Non-Standard Port 82
A web server running on port 82 has issues serving content. Debug and fix the issues. The web service should automatically start at boot time.

Steps:

bash
Copy code
# yum list httpd
# systemctl status httpd.service
# systemctl enable httpd.service
# systemctl status httpd.service
# ls /var/www/html/
file1 file2 file3
# cat /etc/httpd/conf/httpd.conf | grep -i "listen"
Listen 82
# firewall-cmd --permanent --add-port=82/tcp
# firewall-cmd --reload
# firewall-cmd --list-all
# semanage port -l | grep "http"
http_port_t tcp 80, 81, 443, 488, 8008, 8009, 8443, 9000
# semanage port -a -t httpd_port_t -p tcp 82
# semanage port -l | grep "http"
http_port_t tcp 82, 80, 81, 443, 488, 8008, 8009, 8443, 9000
# curl localhost:82/file1
Q4. Create User Accounts with Supplementary Group
Create a group named sysadms and users natasha and harry as members of this group. The user sarah should have a non-interactive shell and should not be a member of sysadms. The password for all users should be trootent.

Steps:

bash
Copy code
# groupadd sysadmin
# useradd -G sysadmin natasha
# useradd -G sysadmin harry
# useradd -s /sbin/nologin sarah
# tail /etc/group
# tail /etc/passwd
# echo "trootent" | passwd --stdin harry
# echo "trootent" | passwd --stdin natasha
# echo "trootent" | passwd --stdin sarah
Q5. Configure a Cron Job that Runs Every 2 Minutes
Create a cron job that runs every 2 minutes and executes logger "EX200 in progress" as the user natasha.

Steps:

bash
Copy code
# systemctl restart crond.service
# crontab -eu natasha
*/2 * * * * logger "EX200 in progress"
:wq!
# systemctl restart crond.service
Q6. Create a Collaborative Directory
Create the directory /home/manager with specific ownership and permissions.

Steps:

bash
Copy code
# mkdir /home/sysadms
# chgrp sysadms /home/sysadms
# ls -ld /home/sysadms
# chmod 2770 /home/sysadms
# ls -ld /home/sysadms
# touch /home/sysadms/file1.txt
# ls -l /home/sysadms/file1.txt
Q7. Configure NTP and Synchronize Time
Synchronize the time of your system with the server utility.example.com.

Steps:

bash
Copy code
# timedatectl
# systemctl restart chronyd.service
# vim /etc/chrony.conf
server utility.example.com iburst
:wq!
# systemctl restart chronyd.service
# timedatectl
# chronyc sources -v
Q8. Configure AutoFS
Configure AutoFS to mount remote user directories via NFS from utility.example.com.

Steps:

bash
Copy code
# getent passwd remoteuserX
# yum install autofs.x86_64 -y
# systemctl enable autofs.service
# systemctl start autofs.service
# vim /etc/auto.master
/rhome/remoteuserX /etc/auto.misc
:wq!
# vim /etc/auto.misc
remoteuserX -fstype=rw,nfs,soft,sync utility.example.com:/rhome/remoteuserX
:wq!
# systemctl restart autofs.service
# su - remoteuserX
# pwd
/rhome/remoteuserX
$ touch file1.txt
Q9. Create a Container Image
Create a container image from the provided link.

Steps:

bash
Copy code
# id athena
# ssh athena@localhost
$ podman login -u admin 
$ password: redhat321
$ wget http://utility.example.com/container/Containerfile
$ podman build -t monitor -f .
$ podman images
localhost/process_files
$ exit
Q10. Create Rootless Container and Run as Service
Create a container named ascii2pdf using the previously created container image monitor and map volumes.

Steps:

bash
Copy code
# mkdir /opt/files
# chown -R athena:athena /opt/files
# mkdir /opt/processed
# chown -R athena:athena /opt/processed
# ssh athena@localhost
$ podman run  -d --name ascii2pdf -v /opt/files:/opt/incoming:Z  -v /opt/processed:/opt/outgoing:Z  localhost/monitor
$ podman ps
$ mkdir /home/athena/.config/systemd/user/
$ cd /home/william/.config/systemd/user/
$ podman generate systemd --name ascii2pdf --files --new
$ ls -lrt
container-ascii2pdf.service
$ systemctl --user daemon-reload
$ systemctl --user enable container-ascii2pdf.service
$ systemctl --user start container-ascii2pdf.service
$ loginctl enable-linger athena
$ loginctl show-user athena
$ systemctl --user stop container-ascii2pdf.service
$ podman ps
$ systemctl --user start container-ascii2pdf.service
$ podman ps
Q11. Create User 'alex' with UID 3456 and Set Password
Create a user alex with UID 3456 and set the password trootent.

Steps:

bash
Copy code
# useradd -u 3456 alex
# echo "trootent" | passwd --stdin alex
# id alex
Q12. Backup and Restore passwd, shadow Files
Perform a backup and restore of /etc/passwd and /etc/shadow.

Steps:

bash
Copy code
# cp /etc/passwd /root/passwd.bak
# cp /etc/shadow /root/shadow.bak
# ls -l /root/passwd.bak
# ls -l /root/shadow.bak

node2

Q13. Recover Root Password on node2
If the root password for node2 is lost, follow the steps to recover it.

Steps:

Reboot the System into Single-User Mode:

During boot, interrupt the GRUB menu by pressing Esc (or Shift depending on the system).
Use the arrow keys to highlight the kernel line (usually the first entry).
Press e to edit the boot parameters.
Find the line starting with linux, and at the end of this line, add single or rd.break to boot into single-user mode.
Press Ctrl + X to boot with this configuration.
Remount the File System in Read/Write Mode:

Once the system boots to the root shell, remount the root filesystem with read and write permissions:
bash
Copy code
mount -o remount,rw /
Change the Root Password:

Use the passwd command to change the root password:
bash
Copy code
passwd root
Enter the new root password when prompted.
Reboot the System:

After successfully changing the root password, reboot the system:
bash
Copy code
reboot
Verify the New Root Password:

After the system boots normally, verify you can log in as root with the new password:
bash
Copy code
su -
Q14. Configure NTP on node2
node2 needs to synchronize its time with a remote NTP server. This configuration is different from node1 as node2 will have its own NTP setup.

Steps:

Check if chronyd is installed:

Ensure that the chrony service is installed and running on node2:
bash
Copy code
yum install chrony -y
Configure the NTP Server:

Edit the /etc/chrony.conf file to specify the NTP server:
bash
Copy code
vim /etc/chrony.conf
Add or modify the line to use the remote server (replace utility.example.com with the appropriate server):
bash
Copy code
server utility.example.com iburst
Start and Enable the NTP Service:

Start the chronyd service and enable it to start at boot:
bash
Copy code
systemctl start chronyd
systemctl enable chronyd
Verify NTP Synchronization:

Check the synchronization status:
bash
Copy code
chronyc tracking
You can also check the sources of time:
bash
Copy code
chronyc sources
Test the Time Sync:

To verify that node2's time is synchronized with the NTP server, check the system time:
bash
Copy code
date
Q15. Configure AutoFS on node1
Configure AutoFS to mount remote directories from the server utility.example.com to node1.

Steps:

Install AutoFS:

Install the autofs package if not already installed:
bash
Copy code
yum install autofs -y
Configure AutoFS Master Map:

Edit the /etc/auto.master file to configure AutoFS:
bash
Copy code
vim /etc/auto.master
Add the following line to the file:
bash
Copy code
/mnt/remote /etc/auto.nfs
Configure AutoFS Map:

Create and edit the /etc/auto.nfs file to specify the NFS mount configuration:
bash
Copy code
vim /etc/auto.nfs
Add the line for the remote mount:
bash
Copy code
remote -fstype=nfs,rw,soft utility.example.com:/mnt/remote
Start and Enable AutoFS:

Enable and start the autofs service:
bash
Copy code
systemctl enable autofs
systemctl start autofs
Test the AutoFS Configuration:

Test the AutoFS configuration by accessing the mounted directory:
bash
Copy code
ls /mnt/remote
Q16. Configure YUM Repositories on node2
Similar to node1, configure node2 to use the same BaseOS and AppStream repositories.

Steps:

Create or Edit YUM Repo Files:

Open or create a file for the repository configuration:
bash
Copy code
vim /etc/yum.repos.d/rhel.repo
Add Repository Entries:

Add the following configuration for the BaseOS and AppStream repositories:
bash
Copy code
[BaseOs]
name=BaseOs
baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS/
gpgcheck=0
enabled=1

[AppStream]
name=AppStream
baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream/
gpgcheck=0
enabled=1
Test YUM Repositories:

Run the following command to test if the repositories are configured correctly:
bash
Copy code
yum repolist
Q17. Enable SELinux and Set Proper Mode
Make sure SELinux is enabled on node2 and is set to the correct mode (either enforcing or permissive depending on the task).

Steps:

Check SELinux Status:

Check the current status of SELinux:
bash
Copy code
getenforce
Enable SELinux if Disabled:

If SELinux is disabled, enable it by editing the /etc/selinux/config file:
bash
Copy code
vim /etc/selinux/config
Change the SELINUX directive to enforcing or permissive:
bash
Copy code
SELINUX=enforcing
Reboot the System:

For the changes to take effect, reboot the system:
bash
Copy code
reboot
Verify SELinux Mode:

After rebooting, verify the SELinux status:
bash
Copy code
getenforce
Q18. Configure a Service to Run at Boot Time
Configure a service to start at boot time on node1.

Steps:

Enable the Service:

For example, enable the httpd service to start at boot:
bash
Copy code
systemctl enable httpd
Verify the Service:

Verify that the service is enabled:
bash
Copy code
systemctl is-enabled httpd
Start the Service:

Start the service if it is not already running:
bash
Copy code
systemctl start httpd
Verify the Service Status:

Check the status of the service:
bash
Copy code
systemctl status httpd
Q19. Create a New Group and Assign Specific Permissions
Create a new group and assign specific permissions to it for a directory on node1.

Steps:

Create a New Group:

Create a group called developers:
bash
Copy code
groupadd developers
Create a Directory:

Create a directory /data/projects:
bash
Copy code
mkdir /data/projects
Assign Group Ownership to the Directory:

Change the group ownership of the directory to developers:
bash
Copy code
chown :developers /data/projects
Set Permissions:

Set the appropriate permissions so that only members of the developers group can read/write:
bash
Copy code
chmod 770 /data/projects
Q20. Install and Configure a Web Server
Install and configure a simple web server (e.g., Apache) to serve static content on node1.

Steps:

Install the Web Server:

Install Apache using yum:
bash
Copy code
yum install httpd -y
Start and Enable the Service:

Start the httpd service and enable it to start on boot:
bash
Copy code
systemctl start httpd
systemctl enable httpd
Create a Test HTML File:

Create a simple index.html file in the web server’s root directory:
bash
Copy code
echo "Hello, RHCSA!" > /var/www/html/index.html
Verify the Web Server:

Check if the web server is working by accessing http://node1.domainX.example.com in a browser or using curl:
bash
Copy code
curl http://node1.domainX.example.com
Q21. Create and Assign Specific Permissions to a File
Create a file and assign specific permissions to it on node2.

Steps:

Create a File:

Create a new file /tmp/testfile:
bash
Copy code
touch /tmp/testfile
Assign Permissions:

Set the permissions so that only the owner can read and write to the file, and others have no access:
bash
Copy code
chmod 600 /tmp/testfile
Verify the Permissions:

Verify the file permissions:
bash
Copy code
ls -l /tmp/testfile
