### **Q1. Configure network and set the static parameters. (configure network in console )**
- IP-ADDRESS= 172.24.10.10
- NETMASK= 255.255.255.0
- GATEWAY= 172.24.10.254
- (DNS) Nameserver= 172.24.10.254 
- Domain Name= domainX.example.com
- hostname= node1.domainX.example.com

### **Q2. Configure YUM repos with the given link (2repos: 1st is BaseOS and 2nd is AppStream)**
- BaseOS http://content.example.com/rhel8.0/x86_64/dvd/BaseOS
- AppSteam http://content.example.com/rhel8.0/x86_64/dvd/AppStream

### **Q3. Debug SELinux - A web server running on non standard port 82 is having issues serving content, Debug and fix the issues.**
- The web server on your system can server all the existing HTML files 
from/var/www/html
- Web service should automatically start at boottime.
- Do not make any changes to these files

### **Q4. Create User accounts with supplementry group.**
- create the group a named "sysadms".
- create users as named "natasha" and "harry", will be the supplementry group "sysadms".
- cerate a user as named "sarah", should have non-interactive shell and it should be not the member of "sysadms".
- password for all users should be "trootent"

### **Q5. Configure a cron job that runs every 2minutes and executes: logger "EX200 in progress" as the user natasha.**
### **Q6. Create a collaborative Directory.**
- Create the Directory "/home/manager" with the following characteristics
- Group ownership of "/home/manager" should go to "sysadms" group
- The directory should have full permission for all members of "sysadms" group but not to the other users except "root"
- Files created in future under "/home/manager" should get the same group ownership

### **Q7. Configure NTP**
- Synchronize time of your system with the server 'utility.example.com'

### **Q8. Configure AutoFS - All remoteuserX home directory is exported via NFS, which is available on utility.example.com(172.24.10.100) and your NFS-exports directory is /home/remoteuserX for remoteuserX**
- remoteuserX's home directory is utility.example.com:/rhome/remoteuserX, where X is your station number and benath as /rhome/remoteuser5
- remoteuserX's home directory should be automounted autofs service.
- home directories must be writable by their users

### **Q9. Create a container image from the provided link.**
- create a container image from "http://utility.example.com/container/Containerfile" name it as 'monitor'with user athena
- login to 'registry.lab.example.com' through "admin" and "redhat321" ->find it out credentials from Instructions page

### **Q10. Create rootless container and do volume mapping which they asked you in the question and run container as a service from normal user account, the service must be enable so it could start automatically after reboot**
- Create a container named as 'ascii2pdf' using the previously created container image from previous question 'monitor'
- Map the '/opt/processed' to container '/opt/outgoing
- Map the '/opt/files' to container '/opt/incoming'
- Create systemd service as container-ascii2pdf.service
- Make service active after all server reboots.

### **Q11. Create user 'alex' with 3456 uid and set the password 'trootent'**

### **Q12. Locate all files owned by user "harry" and copy it under /root/harry-files**

### **Q13. Find a string 'ich' from "/usr/share/dict/words" and put it into /root/lines file.**

### **Q14. create an archive '/root/backup.tar.bz2' of /usr/local directory and compress it with bzip2**

### **Q15. script. Store the search result of all files in the /usr/share directory that is greater than 30k and less than 50k in the /mnt/freespace/search.txt file**

## **Node 2 ---- serverb Instructions**
- Total they’ll give you 2 disks with GPT Partioning scheme ( swap : 19 , LVM : 31 codes in GPT)
- /dev/vda ( don’t make any changes on /dev/vda)
- /dev/vdb ( for swap and LVM )

### **Q15. Reset root user password and make it 'trootent' ( users should answer in console only )**

### **Q16. Configure YUM Repos**
- BaseOS http://content.example.com/rhel8.0/x86_64/dvd/BaseOS
- AppSteam http://content.example.com/rhel8.0/x86_64/dvd/AppStream

### **Q17. Resize a logical Volume -Resize the logical volume "mylv" so that after reboot size should be in between 290MB to 330MB**

### **Q18. Add a swap partition of 512MB and mount it permanently**

### **Q19. Create logical volume and mount it permanently**
- Create a logical volume of name "wshare" from a volume group name "wgroup" physical extents of 16M and logical volume should have size of 50extents
- Mount logical volume with /mnt/wshare and format with ext3 filesystem

### **Q20. Configure System Tuning:**
- Choose the recommended 'tuned' profile for your system and set it as the default






