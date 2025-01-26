# Configuring `dev` Group for Command Permissions on RHEL 8.6

This guide explains how to create the `dev` group on RHEL 8.6, add an existing user (`devuser`) to the group, and configure sudo permissions for specific commands.

## Steps to Configure the `dev` Group

### 1. Create the `dev` Group

Run the following command to create the group:

```bash
sudo groupadd dev
```

- This creates a new group named `dev`.
- Verify the creation by running:
  ```bash
  getent group dev
  ```

---

### 2. Add `devuser` to the `dev` Group

To add `devuser` to the newly created group, use the `gpasswd` command:

```bash
sudo gpasswd -a devuser dev
```

- Verify that `devuser` is now a member of the `dev` group:
  ```bash
  groups devuser
  ```

---

### 3. Configure Sudo Permissions for the `dev` Group

Edit the `/etc/sudoers` file to define specific command permissions for the `dev` group.

#### (a) Open the Sudoers File Safely:

Use the `visudo` command to edit the file:

```bash
sudo visudo
```

#### (b) Add Permissions for the `dev` Group:

Append the following lines to the file:

```plaintext
%dev ALL=NOPASSWD: /bin/sh, /prd/utils/dev-GUI-stop.sh
%dev ALL=NOPASSWD: /bin/sh, /prd/utils/dev-gui.sh
%dev ALL=NOPASSWD: /bin/sh, /prd/utils/source_rate_automation_stop.sh
```

- These lines allow members of the `dev` group to execute the specified commands without entering a password.

#### (c) Save and Exit:

- Save and exit the editor (e.g., use `Ctrl+O`, `Enter`, then `Ctrl+X` in `nano`, or `:wq` in `vim`).

---

### 4. Verify the Configuration

#### (a) Check Group Membership:

Ensure that `devuser` belongs to the `dev` group:

```bash
groups devuser
```

#### (b) Test Command Execution:

Switch to `devuser` and test the commands:

```bash
sudo /bin/sh
sudo /prd/utils/dev-GUI-stop.sh
```

These commands should execute without prompting for a password.

#### (c) List Allowed Commands for `devuser`:

Log in as `devuser` and check the allowed commands:

```bash
sudo -l
```

The output should display the commands permitted by the `dev` group configuration.

---

## Additional Notes

- Always use `visudo` to edit the `/etc/sudoers` file to avoid syntax errors.
- Ensure that the paths (`/prd/utils/...`) in the sudoers file are correct and accessible by `devuser`.

---

