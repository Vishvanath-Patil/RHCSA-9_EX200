# Setting a Static IP Address via `nmtui`

## Steps to Configure Static IP Address

1. Open `nmtui`:
   ```bash
   sudo nmtui
   ```

2. Navigate to **Edit Connection** and press `Enter`.

3. Select **Wired Connection 1** (or your desired connection) and choose **Edit**.

4. Update the following fields:
   - **IPv4 Configuration**: Set to `Manual`.
   - **Addresses**: Enter `172.25.250.10/24`.
   - **Gateway**: Enter `172.25.250.254`.
   - **DNS Server**: Enter `172.25.250.220`.
   - **Search domains**: Enter `lab.example.com`.

5. Enable the option **Ignore automatically obtained DNS Parameters**:
   - Highlight this option and press the `Space Bar` to select it.

6. Click **OK** to save the changes, then select **Back**.

## Steps to Activate the Connection

1. Navigate to **Activate a Connection** and press `Enter`.

2. Perform the following actions:
   - Select **Wired Connection 1** (or your desired connection) and choose **Deactivate**.
   - Select **Wired Connection 1** again and choose **Activate**.

3. Confirm that the changes are applied.

4. Select **Back** to return to the main menu.

## Steps to Set the System Hostname

1. From the main menu, navigate to **Set System Hostname** and press `Enter`.

2. Update the hostname:
   - Enter `servera.lab.example.com` as the new hostname.

3. Click **OK** to save the hostname and return to the main menu.

4. Exit `nmtui`.

## Verification

1. Ping the configured IP address to verify the setup:
   ```bash
   ping 172.25.250.10
   ```

   - The ping should be successful, indicating that the static IP configuration is correct.

