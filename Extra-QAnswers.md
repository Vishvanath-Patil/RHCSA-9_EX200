### **Q1. Give sudo privilege to user or group**

To give `sudo` privileges to a user or a group, modify the `/etc/sudoers` file using `vim` (or another editor).

### For a user:
```bash
# Allow root to run any commands anywhere
root ALL=(ALL) ALL

# Give sudo privileges to username
username ALL=(ALL) ALL

# Allow username to run commands without password
username ALL=(ALL) NOPASSWD: ALL
For a group:
# Allows people in group wheel to run all commands
%wheel ALL=(ALL) ALL

# Allow groupname to run commands without password
%groupname ALL=(ALL) NOPASSWD: ALL
- Make sure to use visudo to safely edit the /etc/sudoers file to avoid syntax errors.
```
---
### **Q2. Set the default max and min days for the password policy**
- To set password expiration policies, modify the /etc/login.defs file.
```bash
# Edit /etc/login.defs:
vim /etc/login.defs
# Add or modify the following lines:
PASS_MAX_DAYS 90       # Maximum password age (90 days)
PASS_MIN_DAYS 30       # Minimum password age (30 days)
PASS_WARN_AGE 5        # Days before password expiration to warn user (5 days)
```
---
### **Q3. Set welcome note for the server**
```bash
# To set a welcome message that appears when a user logs in, edit the /etc/profile file.
Edit /etc/profile:
vim /etc/profile

# Add the following line at the end of the file:
echo "WELCOME TO SERVERA"
Then, reload the profile to apply the changes:
source /etc/profile
```

### **Q4. Question on File Permission and umask**
```bash
# File permissions and umask can be set using the following commands.
# Example Permissions:
# dr-xr-xr-x: Directory with read and execute permissions for everyone (octal 555).
# -r--r--r--: File with read-only permissions for everyone (octal 444).
# Full Permission (octal 777 for directories and 666 for files):

# Directories with full permissions
drwxrwxrwx
#Default Permissions:
# The default permissions for new files are determined by the umask value.
umask 222  # This gives directories with `555` permissions and files with `444` permissions
# Setting umask for a user:
useradd mohit
su - mohit
vim .bashrc

# Add the following line to .bashrc:
umask 222
# Then, apply the changes:

source .bashrc
Create a file and directory to observe the effect:
touch f1
mkdir d1
ls -l
```

### **Q5. Script File to Find Files and Set SGID Permission**
- Steps to create a script and find files based on size:
- Create the script file script.sh inside /bin:
```bash
mkdir -p /mnt/freespace
touch /mnt/freespace/search.txt
vim /bin/script.sh
# Add the following script content:

#!/bin/bash

# Find files from /usr/local that are between 30k and 50k in size
find /usr/local -size +30k -size -50k > /mnt/freespace/search.txt

# Set SGID permission for the directory
chmod g+s /mnt/freespace

wq!

# Make the script executable:
chmod +x /bin/script.sh

# Explanation:
# find /usr/local -size +30k -size -50k: Finds files between 30k and 50k in size under /usr/local and stores the results in search.txt.
# chmod g+s /mnt/freespace: Sets the SGID (Set Group ID) permission on the directory, ensuring that files created within inherit the group ownership.
```
