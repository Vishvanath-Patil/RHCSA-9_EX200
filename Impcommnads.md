## Resolve Subscription issue
```bash
cd /etc/yum/pluginconf.d/
vim subscription-manager.conf
enable = 1
```

## Repo config
```bash
df -kh
mount -a /run/media/servera/RHEL-9-0-0-BaseOS-x86_64
cd /run/media/servera/RHEL-9-0-0-BaseOS-x86_64
ll
cd /etc/yum.repos.d/
ll
vim rhel.repo
file:///run/media/servera/RHEL-9-0-0-BaseOS-x86_64/BaseOS
file:///run/media/servera/RHEL-9-0-0-BaseOS-x86_64/BaseOS
yum clean all
yum repolist
Verify
yum install httpd* -y
systemctl enable --now httpd
systemctl status httpd
```

