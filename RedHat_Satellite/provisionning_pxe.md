# Provisioning a Physical Server Using Red Hat Satellite

This guide explains how to provision a physical server using Red Hat Satellite, detailing each step to ensure a seamless and efficient process.

---

## Prerequisites

### 1. Environment Requirements
- A fully configured Red Hat Satellite server.
- Access to the Satellite web interface with administrative privileges.
- A properly configured Capsule server (if provisioning is through a remote location).

### 2. Physical Server Requirements
- PXE-boot capable network interface.
- IP address allocated from a managed subnet in Satellite.
- Red Hat Enterprise Linux boot media (if PXE is unavailable).

### 3. Network Configuration
- Subnet with DHCP and DNS services configured in Satellite.
- TFTP service enabled and configured on the Capsule server managing the subnet.
- Reverse DNS configured for the server's IP address.

### 4. Satellite Configuration
- Lifecycle environments and content views are set up.
- Host groups defined with relevant provisioning templates and parameters.

---

## Step 1: Configure Satellite for Provisioning

### 1.1 Create a Host Group
1. Navigate to **Configure > Host Groups**.
2. Click **Create Host Group**.
3. Fill in the following fields:
   - **Name**: Descriptive name for the host group (e.g., `WebServers`).
   - **Lifecycle Environment**: Select the appropriate environment.
   - **Content View**: Choose the relevant content view.
   - **Architecture**: Set the architecture (e.g., `x86_64`).
   - **Operating System**: Choose the RHEL version to install.
   - **Partition Table**: Select a partitioning template (or create a custom one).
   - **PXE Loader**: Choose the appropriate option (e.g., `PXELinux BIOS` or `PXEGrub2 UEFI`).
4. Save the host group.

### 1.2 Configure Subnet
1. Navigate to **Infrastructure > Subnets**.
2. Select the subnet managing the server’s IP.
3. Ensure the following services are configured:
   - **DHCP**: Enabled.
   - **DNS**: Enabled.
   - **TFTP**: Enabled.
   - **Boot Mode**: Set to match the server's boot configuration.
4. Save the configuration.

### 1.3 Associate Templates
1. Navigate to **Hosts > Provisioning Templates**.
2. Assign appropriate templates:
   - **Provisioning**: Kickstart or custom.
   - **PXE**: PXELinux or PXEGrub.
   - **Finish**: Post-install scripts (if applicable).
3. Associate templates with the operating system.

---

## Step 2: Add the Physical Server to Satellite

### 2.1 Create a New Host
1. Navigate to **Hosts > Create Host**.
2. Fill in the following details:
   - **Host Group**: Select the previously created host group.
   - **Name**: Enter the server's hostname.
   - **Provisioning Interface**: Set the primary interface:
     - **MAC Address**: Enter the physical server's NIC MAC address.
     - **IP Address**: Assign an IP (or let Satellite allocate).
     - **Subnet**: Select the appropriate subnet.
     - **Domain**: Assign the relevant domain.
3. Confirm PXE Boot configuration:
   - **PXE Loader**: Ensure it matches the server’s boot mode.
4. Save the host.

---

## Step 3: Boot and Install the Server

### 3.1 PXE Boot the Server
1. Connect the server to the network.
2. Configure the server to boot from the network (BIOS/UEFI settings).
3. Start the server.
4. Verify that the server retrieves the bootloader and kickstart file from Satellite or Capsule.

### 3.2 Monitor Installation
1. Navigate to **Hosts > All Hosts** in the Satellite web interface.
2. Confirm the server's status:
   - **Pending Installation**: Indicates that provisioning has started.
   - **Installed**: Indicates successful completion.

---

## Step 4: Post-Installation Configuration

### 4.1 Configure Activation Keys
Activation keys ensure that the server is registered with the correct subscriptions.
1. Navigate to **Content > Activation Keys**.
2. Assign the activation key to the host group.

### 4.2 Assign Additional Policies
Use **Ansible Roles**, **Puppet Classes**, or other configuration management tools integrated with Satellite to apply additional policies.

---

## Step 5: Troubleshooting

### Common Issues
- **PXE Boot Fails**: Verify TFTP, DHCP, and DNS configurations.
- **Provisioning Errors**: Check `/var/log/foreman` and `/var/log/messages` on the Satellite or Capsule server.
- **Kickstart Errors**: Validate the provisioning template and ensure all required repositories are synced.

### Useful Commands
- Restart TFTP service:
  ```bash
  sudo systemctl restart tftp
  ```
- Check DHCP leases:
  ```bash
  cat /var/lib/dhcpd/dhcpd.leases
  ```
- Test PXE files:
  ```bash
  ls /var/lib/tftpboot/pxelinux.cfg
  ```

---

## Additional Resources
- [Red Hat Satellite Documentation](https://access.redhat.com/documentation/en-us/red_hat_satellite/)
- [PXE Boot Troubleshooting Guide](https://access.redhat.com/articles/970193)

---
