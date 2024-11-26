# RHCSA v9 Exam Instructions

## Important Information
Please read the instructions carefully before starting.

---

## Virtual Machines Details
1. **Node 1:**
   - Hostname: `node1.domainX.example.com`
   - IP Address: `172.24.10.10`

2. **Node 2:**
   - Hostname: `node2.domainX.example.com`
   - IP Address: `172.24.10.11`

---

## Key Notes:
1. **Root Password:**
   - For one system, the root password is already set (no reset required).
   - For the other system, the root password needs to be recovered.

2. **Network Configuration:**
   - One system requires network configuration.
   - Networking is already configured on the other system.

3. **NTP Configuration:**
   - Required for **only one** system.

4. **YUM Repository:**
   - Must be configured on **both systems**.

5. **Firewall and SELinux:**
   - Both will be pre-enabled on all systems.

---

## Exam Details
- **Total Questions:** Approximately 22
- **Scope of Tasks:**
  - Basic system administration tasks.
  - Troubleshooting and configuration.
  - Emphasis on critical services and security.

---

## Best Practices:
- Ensure that configurations persist after reboots.
- Validate all configurations using provided tools or commands.
- Do not make unnecessary changes to pre-configured settings.
- Follow the given hostname and IP configurations strictly.

---

Good luck with your RHCSA v9 exam!

# Node1 questions

- `node1` is running by default.
## **Q1. Configure network and set the static parameters.->consider machine  configured as DHCP, need to config it with static parameters**.

## Objective
Configure `node1` with static network parameters, update the hostname, and enable SSH connectivity.

---

## Configuration Steps

### Static Network Configuration
1. Set the following static parameters:
   - **IP Address:** `172.24.10.10`
   - **Netmask:** `255.255.255.0`
   - **Gateway:** `172.24.10.254`
   - **Nameserver:** `172.24.10.254`
   - **Domain Name:** `domainX.example.com`
   - **Hostname:** `node1.domainX.example.com`

2. Execute the following commands:
   ```bash
   # ip a s
   # cat /etc/resolv.conf
   # ip route
   # nmcli connection show
   # nmcli connection modify "Wired connection 1" ipv4.address "172.24.10.10/24" \
       ipv4.dns "172.24.10.254" ipv4.gateway "172.24.10.254" ipv4.method manual
   # nmcli connection reload
   ```
   OR 
---
```bash
nmcli connection modify ens160 ipv4.addresses 192.168.245.50/24
nmcli connection modify ens160 ipv4.gateway 192.168.245.1
nmcli connection modify ens160 ipv4.dns-search "domainX.example.com"
nmcli connection modify ens160 ipv4.dns "8.8.8.8 8.8.4.4"
nmcli connection modify ens160 ipv4.method manual
nmcli connection reload
nmcli connection up ens160
nmcli connection show ens160
# Verify the domain name in the resolv.conf
cat /etc/resolv.conf
#To Set back to DHCP --> nmcli connection modify ens160 ipv4.method auto
```
Hostname Configuration
Verify and set the hostname:
```bash
# hostname
# hostnamectl set-hostname node1.domainX.example.com
```
SSH Configuration
Edit the SSH configuration file:

```bash
# vim /etc/ssh/sshd_config
```
Update the following lines:
```bash
41 PermitRootLogin yes
66 PasswordAuthentication yes
```
Save and exit (:wq).

Restart the SSH service:
```bash
# systemctl restart sshd.service
```
Verification
Test SSH connectivity from the exam-host to node1.domainX.example.com:
```bash
ssh root@node1.domainX.example.com
```
Notes
Ensure all configurations persist after reboot.
Validate connectivity and verify settings using the following commands:
```
ip a s
cat /etc/resolv.conf
ip route
nmcli connection show
```
## **Q2 Configure YUM repos with the given link (2 repos: 1st is BaseOS and 2nd is AppStream)**.
- **BaseOS**: `http://content.example.com/rhel9.0/x86_64/dvd/BaseOS`
- **AppStream**: `http://content.example.com/rhel9.0/x86_64/dvd/AppStream`

## Answer:

### Step 1: Edit the YUM Repository Configuration File
Run the following command to create or edit the repository configuration file:

```bash
# vim /etc/yum.repos.d/rhel.repo
```
Insert the following content into the file:
```bash
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
```
- Save and exit the file by typing :wq then
- Verify Configuration
```bash
# yum list autofs
```
## Q3. Debugging SELinux for Web Server on Non-Standard Port 82

### Steps to Debug and Fix the Issue:

1. **Check the status of `httpd` service:**
   ```bash
   yum list httpd
   systemctl status httpd.service
Ensure httpd starts automatically at boot:
```bash
systemctl enable --now httpd.service
```
Verify the web server's content:
```bash
ls /var/www/html/
# Output:
file1  file2  file3
```
Check the configuration for listening on port 82:
```bash
cat /etc/httpd/conf/httpd.conf | grep -i "listen"
```
Allow port 82 through the firewall:
```bash
firewall-cmd --permanent --add-port=82/tcp
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
firewall-cmd --list-all
vim /etc/httpd/conf/httpd.conf
Change --> Listen 80 to 82 port
```
Check SELinux settings for allowed HTTP ports:
```bash
semanage port -l | grep "http"
# Output:
  http_port_t tcp 80, 81, 443, 488, 8008, 8009, 8443, 9000
```
Add port 82 to SELinux's allowed ports:
```bash
semanage port -a -t httpd_port_t -p tcp 82
```
Verify SELinux settings for port 82:
```bash
semanage port -l | grep "http"
#Ouput:
 http_port_t tcp 82, 80, 81, 443, 488, 8008, 8009, 8443, 9000
```
To Apply the Changes don't forget restart the service
```bash
systectl restart httpd
```
Test the server:
```bash
curl localhost:82/file1
```
## Q4. Create User Accounts with Supplementary Group
1. Create a group named **"sysadms"**.
2. Create users:
   - **natasha** and **harry**: Assign them to the supplementary group **"sysadms"**.
   - **sarah**: Assign a non-interactive shell and ensure it's **not** a member of **"sysadms"**.
3. Set the password for all users to **"trootent"**.

```bash
# Create the group "sysadms"
groupadd sysadms

# Create users "natasha" and "harry" and assign them to the supplementary group "sysadms"
useradd -G sysadms natasha
useradd -G sysadms harry

# Create user "sarah" with a non-interactive shell and ensure it's not part of "sysadms"
useradd -s /sbin/nologin sarah

# Set passwords for all users
echo "trootent" | passwd --stdin harry
echo "trootent" | passwd --stdin natasha
echo "trootent" | passwd --stdin sarah

# Verify the group and user creation
tail /etc/group
tail /etc/passwd
```
## Q5. Configure a cron job that runs every 2 minutes and executes:
- logger "EX200 in progress" as the user natasha.

## Steps:

```bash
# Restart the cron service
systemctl restart crond.service

# Edit the crontab for user natasha
crontab -eu natasha

# Add the following cron job
*/2 * * * * logger "EX200 in progress"

# Save and exit (:wq)

# Restart the cron service again
systemctl restart crond.service
```
Verification:
```bash
#Check the cron job for user natasha:
crontab -l -u natasha

# Verify the logs to ensure the job is running:
tail -f /var/log/messages
```
---
Explanation of the Cron Expression:

*/2 * * * * logger "EX200 in progress"

*/2: Runs the job every 2 minutes.
* (1st position): Every hour.
* (2nd position): Every day of the month.
* (3rd position): Every month.
* (4th position): Every day of the week.
logger "EX200 in progress": Logs the message "EX200 in progress" to the system logs.
---
## Q6. Create a Collaborative Directory

## Task Description:
- Create the directory **`/home/manager`** with the following characteristics:
  1. Group ownership of **`/home/manager`** should go to the **"sysadms"** group.
  2. The directory should have full permissions for all members of the **"sysadms"** group, but no permissions for other users except **"root"**.
  3. Files created in the directory should automatically inherit the **"sysadms"** group ownership.

---

## Steps:

```bash
# Step 1: Create the directory
mkdir /home/manager

# Step 2: Change the group ownership to "sysadms"
chgrp sysadms /home/manager

# Step 3: Verify the group ownership
ls -ld /home/manager
# Output:
# drwxr-xr-x 2 root sysadms 4096 <date> /home/manager

# Step 4: Set permissions for the directory with a setgid bit
chmod 2770 /home/manager

# Step 5: Verify the permissions
ls -ld /home/manager
# Output:
# drwxrws--- 2 root sysadms 4096 <date> /home/manager

# Step 6: Create a test file in the directory
touch /home/manager/file1.txt

# Step 7: Verify the group ownership of the new file
ls -l /home/manager/file1.txt
# Output:
# -rw-r--r-- 1 root sysadms 0 <date> /home/manager/file1.txt
```
## Q7. Configure NTP - Synchronize time of your system with the server 'utility.example.com'

## Steps:

```bash
# Step 1: Check the current system time and NTP status
timedatectl

# Step 2: Restart the chrony service
systemctl restart chronyd.service

# Step 3: Edit the chrony configuration file
vim /etc/chrony.conf

# Step 4: Add the NTP server entry
server utility.example.com iburst

# Step 5: Save and exit the editor (:wq!)

# Step 6: Restart the chrony service to apply the changes
systemctl restart chronyd.service

# Step 7: Verify the system time and NTP synchronization
timedatectl

# Step 8: Check the NTP sources
chronyc sources -v
```
```bash
#Expected Outputs:
#After Step 7
timedatectl
Output:
Local time: <current_time>
Universal time: <current_utc_time>
RTC time: <rtc_time>
Time zone: <timezone>
NTP synchronized: yes
NTP service: active

#After Step 8 (chronyc sources -v):
Output:
210 Number of sources = 1
.utility.example.com           1   2   3    3   0.234   0.002   0.002
```
## Q8. Configure AutoFS

### Task Description:
- All `remoteuserX` home directories should be exported via NFS, which is available on `utility.example.com` (IP: 172.24.10.100).
- Your NFS exports directory is `/home/remoteuserX` for each `remoteuserX`.
- `remoteuserX`'s home directory should be automatically mounted using the AutoFS service.
- Home directories must be writable by their respective users.

---

## Steps:

```bash
# Step 1: Verify the existence of `remoteuserX`
getent passwd remoteuserX

# Step 2: Install AutoFS package
yum install autofs.x86_64 -y

# Step 3: Enable and start the AutoFS service
systemctl enable autofs.service
systemctl start autofs.service

# Step 4: Configure AutoFS in the `auto.master` file
vim /etc/auto.master

# Add the following entry:
/rhome/remoteuserX /etc/auto.misc

:wq!

# Step 5: Configure AutoFS in the `auto.misc` file
vim /etc/auto.misc

# Add the following line:
remoteuserX -fstype=rw,nfs,soft,sync utility.example.com:/rhome/remoteuserX

:wq!

# Step 6: Restart the AutoFS service
systemctl restart autofs.service

# Verification:
# Switch to the `remoteuserX` account
su - remoteuserX

# Verify the mount point
pwd

# Should show:
/rhome/remoteuserX

# Test if the home directory is writable by creating a new file
$ touch file1.txt
```
```bash
su - remoteuserX
pwd
# Output should be:
/rhome/remoteuserX

Test by creating a new file:
$ touch file1.txt
```
---
```bash
Explanation:
Verify the existence of remoteuserX:

The getent passwd remoteuserX command checks if the user remoteuserX exists on the system.
Install AutoFS:

The yum install autofs.x86_64 -y command installs the AutoFS service, which will help in automatically mounting remote home directories when accessed.
Enable and start AutoFS:

The systemctl enable autofs.service ensures that the AutoFS service starts automatically at boot, and the systemctl start autofs.service starts the service immediately.
Configure AutoFS in auto.master:

The entry /rhome/remoteuserX /etc/auto.misc in the auto.master file tells AutoFS to use the /etc/auto.misc configuration file when mounting directories for remoteuserX.
Configure AutoFS in auto.misc:

The line remoteuserX -fstype=rw,nfs,soft,sync utility.example.com:/rhome/remoteuserX in the auto.misc file specifies the properties for mounting the remote user home directories over NFS.
Restart the AutoFS service:

Restarting the AutoFS service applies the new configurations.
Expected Outputs:
After Step 5, verify if the user home directory is mounted properly:
```
---
## Q9. Create a Container Image from the Provided Link

### Task Description:
- Create a container image from the **`Containerfile`** available at the link `http://utility.example.com/container/Containerfile`, and name the image as **`process_files`**.
- Login to the registry at `registry.lab.example.com` using the username **`admin`** and password **`redhat321`** (credentials available from the additional info page).

---

## Steps:

```bash
# Step 1: Check the current user ID for athena
id athena

# Step 2: SSH into athena@localhost
ssh athena@localhost

# Step 3: Login to the container registry using podman
$ podman login -u admin
$ password: redhat321

# Step 4: Download the Containerfile from the provided link
$ wget http://utility.example.com/container/Containerfile

# Step 5: Build the container image using podman
$ podman build -t process_files -f .

# Step 6: List the images to verify the image creation
$ podman images
# Output should show: 
# localhost/process_files

# Step 7: Exit the SSH session
$ exit
```
---
```bash
Expected Outputs:
After Step 6, when you run podman images, the output should look like:
plaintext
Copy code
REPOSITORY                TAG     IMAGE ID       CREATED         SIZE
localhost/process_files    latest  <image_id>     <timestamp>     <size>
```
###Explanation:
- id athena: The id command is used to check the user details for athena. This ensures that the user exists and the permissions are set correctly before proceeding.
- ssh athena@localhost: You SSH into the athena user account on the local machine to perform the necessary steps for building the container image.
- podman login -u admin: This command logs you into the container registry using the provided username (admin) and password (redhat321).
- wget http://utility.example.com/container/Containerfile: Downloads the Containerfile from the specified URL to the local machine.
- podman build -t process_files -f .: Builds the container image using the Containerfile and tags it as process_files.
- podman images: This command lists the local container images. You should see the newly created image localhost/process_files.
- exit: Exits the SSH session after completing the task.
---
## Q10. Create Rootless Container and Do Volume Mapping

## Task Description:
1. **Create a container named `ascii2pdf`** using the previously created container image `monitor`.
2. **Map** the local directories `/opt/files` and `/opt/processed` to the container directories `/opt/incoming` and `/opt/outgoing` respectively.
3. **Create a systemd service** called `container-ascii2pdf.service` to manage the container as a service.
4. **Enable the service** so it starts automatically after a reboot.
  
---

### Steps:

```bash
# Step 1: Create and set up the local directories
mkdir /opt/files
chown -R athena:athena /opt/files

mkdir /opt/processed
chown -R athena:athena /opt/processed

# Step 2: SSH into athena user account
ssh athena@localhost

# Step 3: Run the container with volume mapping
$ podman run -d --name ascii2pdf -v /opt/files:/opt/incoming:Z -v /opt/processed:/opt/outgoing:Z localhost/monitor

# Step 4: Verify that the container is running
$ podman ps

# Step 5: Create the systemd user directory if not already present
$ mkdir -p /home/athena/.config/systemd/user/

# Step 6: Generate the systemd service for the container
$ cd /home/athena/.config/systemd/user/
$ podman generate systemd --name ascii2pdf --files --new

# Step 7: Verify the generated systemd service file
$ ls -lrt
# Output should show:
# container-ascii2pdf.service

# Step 8: Reload systemd user services
$ systemctl --user daemon-reload

# Step 9: Enable the systemd service to start on boot
$ systemctl --user enable container-ascii2pdf.service

# Step 10: Start the systemd service
$ systemctl --user start container-ascii2pdf.service

# Step 11: Enable lingering for the athena user to allow the service to run after logout
$ loginctl enable-linger athena

# Step 12: Verify user lingering is enabled
$ loginctl show-user athena

# Step 13: Verify the status of the service
$ systemctl --user stop container-ascii2pdf.service
$ podman ps
$ systemctl --user start container-ascii2pdf.service
$ podman ps
```
---
```bash
Expected Outputs:
After Step 4 (podman ps):

CONTAINER ID   IMAGE                         COMMAND               CREATED         STATUS         PORTS   NAMES
<container_id> localhost/monitor              <command>             <timestamp>     Up <time>               ascii2pdf
After Step 12 (loginctl show-user athena):
```
```bash
User=athena
Linger=yes
After Step 13 (podman ps):


CONTAINER ID   IMAGE                         COMMAND               CREATED         STATUS         PORTS   NAMES
<container_id> localhost/monitor              <command>             <timestamp>     Up <time>               ascii2pdf
```
### Explanation:
### Creating and setting up local directories:

- /opt/files and /opt/processed are created and ownership is set to the athena user. These directories will be mapped to the container’s incoming and outgoing directories respectively.
Running the container with volume mapping:

- The podman run command starts the ascii2pdf container with volume mappings (/opt/files:/opt/incoming and /opt/processed:/opt/outgoing). The :Z flag ensures that SELinux contexts are applied correctly.
Generating a systemd service for the container:

- The podman generate systemd command generates a systemd service unit file for the container, which will manage the container lifecycle using systemctl.
Enabling and starting the systemd service:

- The service is enabled using systemctl --user enable to ensure it starts on boot. It is then started using systemctl --user start.
Enabling lingering for the athena user:

- The loginctl enable-linger athena command allows the athena user to have persistent services that continue running after they log out.
Verification:

- After the service is started, systemctl --user stop and systemctl --user start verify that the service can be stopped and started successfully, while podman ps ensures the container is running.
---
## Q11. Create User 'alex' with UID 3456 and Set the Password 'trootent'

## Task Description:
- Create a user **`alex`** with the UID **3456**.
- Set the password for **`alex`** to **`trootent`**.

---

## Steps:

```bash
# Step 1: Create the user 'alex' with UID 3456
useradd -u 3456 alex

# Step 2: Set the password for 'alex'
echo "trootent" | passwd --stdin alex

# Step 3: Verify the user creation and UID
id alex
Output: uid=3456(alex) gid=3456(alex) groups=3456(alex)
```
## Q12. Locate All Files Owned by User "harry" and Copy Them Under /root/harry-files

## Task Description:
- Locate all files owned by user **`harry`** and copy them to the directory **`/root/harry-files`**.

---

## Steps:

```bash
# Step 1: Create the target directory for storing files
mkdir /root/harry-files

# Step 2: Find all files owned by user 'harry' and copy them to /root/harry-files
find / -user harry -exec cp -rvfp {} /root/harry-files \;

# Step 3: Verify the files copied
ls -a /root/harry-files
```
## Q13. Find a String 'ich' from "/usr/share/dict/words" and Put It into /root/lines File

## Task Description:
- Search for the string **`ich`** in the file **`/usr/share/dict/words`** and store the matching lines in the file **`/root/lines`**.

---

## Steps:

```bash
# Step 1: Search for the string 'ich' in /usr/share/dict/words and save the result to /root/lines
grep "ich" /usr/share/dict/words > /root/lines

# Step 2: View the contents of /root/lines
cat /root/lines
```
## Q14. Create an Archive '/root/backup.tar.bz2' of /usr/local Directory and Compress It with bzip2

### Task Description:
- Create a compressed archive of the **`/usr/local`** directory and save it as **`/root/backup.tar.bz2`** using **bzip2** compression.

---

## Steps:

```bash
# Step 1: Install bzip2 if not already installed
yum install bzip2.x86_64 -y

# Step 2: Create a compressed tar archive of /usr/local
tar cfvj /root/backup.tar.bz2 /usr/local
```
---
### Explanation:
- yum install bzip2.x86_64 -y: Installs the bzip2 package, which is required for compression.
- tar cfvj /root/backup.tar.bz2 /usr/local: The tar command creates a .tar.bz2 archive:
- c – Create a new archive.
- f – Specifies the file name for the archive.
- v – Verbose output (optional, for displaying files being archived).
- j – Use bzip2 compression.
- The resulting archive will be stored at /root/backup.tar.bz2.

#### Expected Output:
- After running the tar command, a /root/backup.tar.bz2 file will be created containing the /usr/local directory.
---

## Q15. Script to Store Search Result of Files in /usr/share Directory Greater Than 30k and Less Than 50k in /mnt/freespace/search.txt File

### Task Description:
- Write a script that searches for all files in the **`/usr/share`** directory that are greater than 30k and less than 50k in size, and stores the search results in **`/mnt/freespace/search.txt`**.

---

## Steps:

```bash
# Step 1: Create a new script file
vim test.sh

# Step 2: Write the script
#!/bin/bash
find /usr/share/ -uid 0 -size +30k -size -50k >/mnt/freespace/search.txt

# Step 3: Save and exit the editor (:wq!)

# Step 4: Make the script executable
chmod +x test.sh

# Step 5: Run the script
bash test.sh

# Step 6: Check the content of the search results
cat /mnt/freespace/search.txt
```
---
### Explanation:
- vim test.sh: Opens the vim editor to create a new script file called test.sh.
- #!/bin/bash: This shebang line ensures that the script runs with the Bash shell.
- find /usr/share/ -uid 0 -size +30k -size -50k >/mnt/freespace/search.txt: The find command searches for files in the /usr/share directory that are:
- Owned by root (-uid 0).
- Between 30k and 50k in size (-size +30k -size -50k).
- The results are redirected to /mnt/freespace/search.txt.
- chmod +x test.sh: Makes the script executable.
- bash test.sh: Executes the script.
- cat /mnt/freespace/search.txt: Displays the contents of the search.txt file, showing the list of matching files.
#### Expected Output:
- After running cat /mnt/freespace/search.txt, the output will display the list of files in /usr/share that are greater than 30k and less than 50k in size.
---
# Node 2 Questions
## Q16. Reset root user password and make it 'trootent'

### Steps:

#### 1. Reboot the System
- Press **Ctrl + Alt + Del** to reboot the system.

#### 2. Select Rescue Mode
- When the GRUB menu appears:
  - Select **Rescue Mode** or **Troubleshooting Mode**.
- If **Rescue Mode** is not visible:
  - Highlight the default boot entry and press **`e`** to edit boot parameters.

#### 3. Modify Kernel Parameters
- Locate the line starting with `linux` or `linux16`.
- Add the following at the end of the line:
  ```bash
  rd.break
- Press Ctrl + X or F10 to boot into the rescue shell.
#### 4. Remount Root Filesystem
- In the rescue shell, remount the root filesystem in read-write mode:
```bash
mount -o remount,rw /sysroot
```
#### 5. Access the Root Filesystem
- Change into the root filesystem:
```bash
chroot /sysroot
```
#### 6. Reset the Root Password
- Set the new root password:
```bash
passwd
```
When prompted, set the password as trootent.
#### 8. Trigger SELinux Relabeling
- Create a file to trigger SELinux relabeling during the next boot:

```bash
touch /.autorelabel
```
#### 8. Exit and Reboot
- Exit the chroot environment:
```bash
exit
```
- Exit the rescue shell:
```bash
exit
```
- The system will reboot, and SELinux will relabel the filesystem.
---
#### 9. Verify Root Access
- After rebooting, log in as the root user using the new password:
Username: root
Password: trootent

### Important Notes:
### SELinux Relabeling:
- The relabeling process may take some time, depending on the size of your filesystem.
## Troubleshooting Mode:
- If a dedicated "Rescue" option is not visible, appending rd.break to the default boot entry achieves the same result.
---
## **Q17 Configure YUM repos with the given link (2 repos: 1st is BaseOS and 2nd is AppStream)**.
- **BaseOS**: `http://content.example.com/rhel9.0/x86_64/dvd/BaseOS`
- **AppStream**: `http://content.example.com/rhel9.0/x86_64/dvd/AppStream`

## Answer:

### Step 1: Edit the YUM Repository Configuration File
Run the following command to create or edit the repository configuration file:

```bash
# vim /etc/yum.repos.d/rhel.repo
```
Insert the following content into the file:
```bash
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
```
- Save and exit the file by typing :wq then
- Verify Configuration
```bash
# yum list autofs
```
---
## Q18. Resize a Logical Volume

### Task Description:
Resize the logical volume **"mylv"** so that after reboot, its size should be between **290MB** and **330MB**.

### Steps:

#### 1. Check the Current Logical Volumes
- List all logical volumes:
  ```bash
  lvs
  ```
#### 2. Check the Filesystem Usage
- Display disk usage and filesystem type:
```bash
df -hT
```
#### 3. Resize the Logical Volume
- Use lvextend to resize the logical volume "mylv" to 310MB:
```bash
lvextend -L 310M /dev/myvg/mylv
```
#### 4. Resize the Filesystem
- After resizing the logical volume, resize the filesystem to match the new size:
```bash
resize2fs /dev/mapper/myvg-mylv
```
#### 5. Verify the Logical Volume Size
- Check the logical volumes again to verify the new size:
```bash
lvs
```
#### 6. Verify Disk Usage
- Verify the disk usage and the updated filesystem size:
```bash
df -hT
```
---
## Q19. Add a Swap Partition of 512MB and Mount it Permanently

### Task Description:
Add a swap partition of **512MB** and mount it permanently.

### Steps:

#### 1. Create the Swap Partition
- Use `gdisk` to create a new partition on `/dev/vdb`:
  ```bash
  gdisk /dev/vdb
  ```
- Command (? for help): n to create a new partition
- Partition number (2-128, default 2): Press Enter
- First sector (34-2047, default = 34) or {+-}size{KMGTP}: Press Enter
- Last sector (34-2047, default = 2047) or {+-}size{KMGTP}: +512M
- Hex code or GUID (L to show codes, Enter = 8300): Press Enter
#### 2. Set the Partition Type to Swap
- Change the partition type to Linux swap:
```bash
Command (? for help): t
Partition number (1-2): 2
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): 8200
```
- Save and write the changes:
Command (? for help): w
#### 3. Settle Udev Rules and Reboot
- Settle udev changes:
```bash
udevadm settle
```
- Reboot the system to apply changes:
```bash
reboot
```
#### 4. Verify the Partition
- Verify the new partition using lsblk:
```bash
lsblk
```
#### 5. Create Swap Space
- Format the newly created partition as swap:
```bash
mkswap /dev/vdb2
```
#### 6. Get the UUID of the Swap Partition
- Copy the UUID of the swap partition:
```bash
blkid /dev/vdb2
Example output:
UUID="e5a95dd4-0417-4229-a499-92b29fe9f201"
```
7. Edit /etc/fstab for Permanent Mount
- Open /etc/fstab in a text editor:
```bash
vim /etc/fstab
```
- Add the following line to automatically mount the swap partition on boot:
```bash
UUID=e5a95dd4-0417-4229-a499-92b29fe9f201 swap swap defaults 0 0
```
- Save and exit the editor (:wq!).
#### 8. Mount All Filesystems
- Mount all filesystems defined in /etc/fstab:
```bash
mount -a
```
#### 9. Enable Swap
- Activate the swap partition:
```bash
swapon /dev/vdb2
```
#### 10. Verify the Swap
- Verify swap is active:
```bash
swapon -s
```
- Alternatively, you can use:
```bash
swapon -d
```
#### 11. Check Memory Usage
Check the system's memory usage including swap:
```bash
free -m
```
---
## Q20. Create a Logical Volume of Name "newlv" from a Volume Group Name "newvg"

### Task Description:
Create a logical volume named **"newlv"** from a volume group named **"newvg"** with physical extents of **16M** and the logical volume size of **50 extents**.

### Steps:

#### 1. Create a New Partition for LVM
- Use `gdisk` to create a new partition on `/dev/vdb`:
  ```bash
  gdisk /dev/vdb
  ```
- Command (? for help): n to create a new partition
- Partition number (3-128, default 3): 3
- First sector (34-2047, default = 34) or {+-}size{KMGTP}: Press Enter
- Last sector (34-2047, default = 2047) or {+-}size{KMGTP}: +1024M
- Hex code or GUID (L to show codes, Enter = 8300): Press Enter
#### 2. Set the Partition Type to Linux LVM
- Change the partition type to Linux LVM (code: 8e00):
```bash
Command (? for help): t
Partition number (1-3): `3`
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): `8e00`
```
- Save the partition table:
```bash
Command (? for help): w
```
#### 3. Settle Udev Rules
Settle udev changes:
```bash
udevadm settle
```
#### 4. Create Physical Volume (PV)
- Create the physical volume using /dev/vdb3:
```bash
pvcreate /dev/vdb3
```
#### 5. Create Volume Group (VG)
- Create the volume group newvg with a physical extent size of 16M:
```bash
vgcreate -s 16M newvg /dev/vdb3
```
#### 6. Verify Volume Group
- Verify the volume group creation:
```bash
vgdisplay
```
#### 7. Create Logical Volume (LV)
- Create a logical volume named newlv with a size of 50 extents:
```bash
lvcreate -n newlv -l 50 newvg
```
#### 8. Verify Logical Volume
- Verify the creation of the logical volume:
```bash
lvs
```
#### 9. Format the Logical Volume
- Format the logical volume with the ext3 filesystem:
```bash
mkfs -t ext3 /dev/mapper/newvg-newlv
```
#### 10. Get UUID of the Logical Volume
- Get the UUID of the new logical volume:
```bash
blkid
```
#### 11. Create Mount Point
- Create a directory to mount the new logical volume:
```bash
mkdir /mnt/newlv
```
#### 12. Edit /etc/fstab for Permanent Mount
- Open /etc/fstab in a text editor:
```bash
vim /etc/fstab
```
- Add the following line to /etc/fstab to mount the logical volume automatically on boot:
```bash
UUID=<UUID-of-newlv> /mnt/newlv ext3 defaults 0 0
Replace <UUID-of-newlv> with the actual UUID obtained from blkid.
Save and exit the editor (:wq!).
```
#### 13. Mount All Filesystems
- Mount all filesystems defined in /etc/fstab:
```bash
mount -a
```
#### 14. Verify Mount
Verify that the new logical volume is mounted:
```bash
df -hT
```
## Q21. Configure System Tuning

### Task Description:
Choose the recommended **'tuned'** profile for your system and set it as the default.

### Steps:

#### 1. List Available Tuned Packages
- Use the following command to list the available tuned packages:
  ```bash
  yum list tuned
  ```
#### 2. Restart the Tuned Service
- Restart the tuned service to ensure the latest changes take effect:
```bash
systemctl restart tuned.service
```
#### 3. Check the Current Active Profile
- Check the current active profile:
```bash
tuned-adm active
Output:
sql
Copy code
Current active profile: balanced
```
#### 4. Get the Recommended Profile
- Get the recommended profile for your system:
```bash
tuned-adm recommend
#Output:
virtual-guest
```
#### 5. Set the Virtual-Guest Profile as Active
- Set the virtual-guest profile as the active profile:
```bash
tuned-adm profile virtual-guest
```
#### 6. Verify the Active Profile
- Verify that the virtual-guest profile is now active:
```bash
tuned-adm active
Output:
- Current active profile: virtual-guest
```
