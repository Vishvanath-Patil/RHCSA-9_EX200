## Resolve Subscription issue
```bash
cd /etc/yum/pluginconf.d/
vim subscription-manager.conf
enable = 1
```

## Repo config
```bash
mount -a /run/media/servera/RHEL-9-0-0-BaseOS-x86_64
cd /run/media/servera/RHEL-9-0-0-BaseOS-x86_64
ll
cd /etc/yum.repos.d/
```
