```bash
Q9. Create a container image from the provided link.
    a. create a container image from 
"http://utility.example.com/container/Containerfile" name it as 'process_files'
    login to 'registry.lab.example.com' through "admin" and "redhat321"->find it out credentials from additional info page

switch to user root 
su - root
athena will be the existing user.

ssh athena@localhost 

podman login registry.lab.example.com
username: admin
password: redhat321

-------------> Download the Image <--------------

wget <imagelink>

-------------> Build the Image <-----------------

podman build -t <Image-Tag> -f .

podman build -t proccess_files -f .

---------> Check Podman Images

podman images
localhost/process_files (Output will get )
-----------------------------------------------Extra
podman pull docker.io/centos/httpd

===+++==========================================================================================================
 Q10. Create rootless container and do volume mapping which they asked you in the question and run container as a service from normal user account, the service must 
be enable so it could start automatically after reboot.
  a.   Create a container named as 'ascii2pdf'  using the previously created container image from previous question 'monitor'
  b.   Map the '/opt/files' to container '/opt/incoming'
  c.   Map the '/opt/processed' to container '/opt/outgoing'
  d.   Create systemd service as container-ascii2pdf.service
  e.   Make service active after all server reboots.

-----THIS has to done by root user --------------------
mkdir /opt/files
mkdir /opt/processed 

ls -ld /opt/files

chown -R athena:athena /opt/files
chown -R athena:athena /opt/processed

-------------This has to done by athena user ---------------

ssh athena@localhost

podman run -d --name ascii2pdf -v /opt/files:/opt/incoming:Z -v /opt/processed:/opt/outgoing:Z localhost/process_files

podman images 

podman ps 

mkdir -p .config/systemd/user

cd .config/systemd/user

podman generate systemd --name ascii2pdf --file --new

ls 

systemctl --user daemon-reload     | Note: If you are facing issue --> loginctl enable-linger athena
systemctl --user start container.ascii2pdf.service 
systemctl --user enable container.ascii2pdf.service
systemctl --user status container.ascii2pdf.service


Q.22 set tuned recomonded profile

systemctl status tuned

yum install tuned.service

systemctl enable --now tuned

tuned-adm active

tuned-adm recomond 

tuned-adm profile virtual-guest

tuned-adm 
```
