# Graylog Sidecar Installation and Configuration with Ansible

## Overview
This Ansible playbook automates the installation and configuration of the Graylog Sidecar on a Windows system. It downloads the Graylog Sidecar installer, installs it, fetches the necessary configuration from a Graylog server, applies the configuration, and ensures the service is operational.

## Prerequisites

1. **Ansible Control Node**
   - Ensure you have Ansible installed on your control node.
   - The `winrm` connection plugin should be configured for managing Windows hosts.

2. **Target Windows System**
   - Ensure WinRM is enabled and configured to allow remote connections.
   - Administrator privileges are required.

3. **Graylog Server**
   - A running Graylog server accessible from the Windows system.
   - Obtain the Graylog API token (`graylog_api_token`).
   - Note the Graylog server's IP address (`graylog_ip_addr`).

4. **Dependencies**
   - Ensure the following Ansible modules are available:
     - `win_get_url`
     - `win_package`
     - `win_file`
     - `win_copy`
     - `win_lineinfile`
     - `win_service`
     - `win_service_info`

## Variables
The playbook uses the following variables:

- `graylog_sidecar_executable`: URL to the Graylog Sidecar installer.
  - Default: `https://github.com/Graylog2/collector-sidecar/releases/download/1.5.0/graylog_sidecar_installer_1.5.0-1.exe`
- `graylog_ip_addr`: IP address of the Graylog server.
- `graylog_api_token`: API token for authenticating with the Graylog server.

## How to Use

1. **Prepare Inventory File**
   - Create an inventory file specifying the target Windows host:

     ```ini
     [windows]
     target_host ansible_host=192.168.x.x ansible_user=Administrator ansible_password=YourPassword ansible_connection=winrm
     ```

2. **Update Variables**
   - Update the `graylog_ip_addr` and `graylog_api_token` variables in the playbook to match your Graylog server settings.

3. **Run the Playbook**
   - Execute the playbook with the following command:

     ```bash
     ansible-playbook -i inventory.ini graylog_sidecar.yml
     ```

4. **Verify Installation**
   - Ensure the Graylog Sidecar service is running on the target Windows host.
   - Check the `sidecar.yml` configuration file under `C:\Program Files\Graylog\sidecar\` to verify correct settings.

## Playbook Tasks Breakdown

1. **Download Installer**
   - Uses the `win_get_url` module to download the Sidecar executable from GitHub.

2. **Install Sidecar**
   - Installs the Sidecar using `win_package` with quiet installation arguments.

3. **Prepare Configuration Directory**
   - Ensures the Sidecar configuration directory exists using `win_file`.

4. **Fetch Configuration**
   - Downloads the Sidecar configuration file from the Graylog server using `win_get_url`.

5. **Apply Configuration**
   - Copies the downloaded configuration file to the Sidecar directory.
   - Configures the `server_url` and `server_api_token` settings in the `sidecar.yml` file using `win_lineinfile`.

6. **Manage Service**
   - Starts the Graylog Sidecar service and sets it to auto-start.
   - Verifies the service status using `win_service_info`.
   - Fails the playbook execution if the service is not running.

## Troubleshooting

- **Service Not Starting**
  - Check the `sidecar.yml` configuration file for errors.
  - Ensure the Graylog server is reachable from the Windows system.

- **Connection Issues**
  - Verify WinRM is properly configured on the target Windows host.
  - Test connectivity from the Ansible control node using `ansible -m ping -i inventory.ini windows`.

## Notes

- Modify the playbook to use a different Sidecar version by updating the `graylog_sidecar_executable` variable.
- Ensure sensitive information like the API token is handled securely (e.g., using Ansible Vault).

By using this playbook, you can simplify the deployment and configuration of the Graylog Sidecar across multiple Windows systems in your environment.

