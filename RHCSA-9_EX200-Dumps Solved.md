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
firewall-cmd --reload
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
