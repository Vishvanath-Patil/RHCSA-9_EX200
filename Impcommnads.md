# Subscription Manager Configuration

## Configuration Settings:

### `enabled=0`:
Disables the subscription-manager plugin, meaning YUM/DNF won't check for Red Hat subscriptions automatically.

#### Why do this?
To prevent automatic subscription checks or repository access, which may be unnecessary for non-Red Hat systems, or if you want to manually control subscriptions.

### `disable_system_repos=0`:
Keeps third-party or custom repositories enabled, even when subscription-manager is triggered.

#### Why do this?
To allow access to non-Red Hat repositories while still using the system with subscription-manager for Red Hat updates. It ensures flexibility in managing custom or external repositories.

## In Short:
These settings are used when you want to avoid automatic Red Hat subscription checks and keep non-Red Hat repositories active.

---

## Resolve Subscription Issue
If you wish to disable the subscription-manager plugin and prevent automatic subscription checks, you can configure it as follows:

```bash
cd /etc/yum/pluginconf.d/
vim subscription-manager.conf
```
### Set the following line:
```bash
enabled=0
- This will disable the subscription-manager plugin and stop automatic subscription checks during YUM/DNF operations.
```
---
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

