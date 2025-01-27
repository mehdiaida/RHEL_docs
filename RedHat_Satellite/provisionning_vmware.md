# Provisioning a Virtual Machine on VMware Using Red Hat Satellite

This guide details the steps required to provision a virtual machine (VM) on VMware using Red Hat Satellite. The process includes integrating VMware with Satellite, setting up required configurations, and deploying a VM.

---

## Prerequisites

### 1. Infrastructure Requirements
- **VMware vCenter Server** with an accessible API.
- Red Hat Satellite server (fully configured).
- Network configurations for the VM (subnet, DHCP, DNS).
- Lifecycle environments and content views set up in Satellite.

### 2. Access and Permissions
- VMware account with sufficient permissions to create and manage VMs.
- Administrative access to Red Hat Satellite.

### 3. VMware Integration Plugin
Ensure the VMware compute resource plugin is installed on the Satellite server. Verify using:
```bash
rpm -qa | grep foreman-compute
```
If not installed, add the required plugin:
```bash
sudo dnf install -y tfm-rubygem-foreman-compute
```

Restart the Satellite service:
```bash
sudo systemctl restart foreman
```

---

## Step 1: Configure VMware as a Compute Resource

### 1.1 Add a Compute Resource
1. Navigate to **Infrastructure > Compute Resources**.
2. Click **Create Compute Resource**.
3. Fill in the details:
   - **Name**: Provide a descriptive name (e.g., `vCenter_Cluster1`).
   - **Provider**: Select `VMware`.
   - **Server**: Enter the FQDN or IP address of the vCenter Server.
   - **Username/Password**: Provide VMware credentials.
   - **Datacenter**: Select the datacenter where VMs will be provisioned.
4. Test the connection to ensure the settings are correct.
5. Save the compute resource.

### 1.2 Import Clusters and Templates
1. Navigate to the newly created compute resource.
2. Click **Clusters** and **Import Clusters** to make them available.
3. Click **Templates** and **Import Templates** to use pre-configured VM templates.

---

## Step 2: Configure Provisioning Templates

### 2.1 Create a Provisioning Template
1. Navigate to **Hosts > Provisioning Templates**.
2. Click **Create Template**.
3. Define the template:
   - **Name**: Enter a descriptive name (e.g., `VMware_RHEL_Kickstart`).
   - **Type**: Select `Provision`.
   - **Content**: Use a Kickstart configuration or customize as needed.
4. Associate the template with the operating system:
   - **Operating Systems** > Select OS > **Templates**.
   - Assign the template as the provisioning method.
5. Save the template.

### 2.2 Ensure PXE and Finish Templates Are Configured
1. Associate PXE and Finish templates if required.
2. Enable PXE Boot for networks managed by Satellite.

---

## Step 3: Define a Host Group

### 3.1 Create a Host Group
1. Navigate to **Configure > Host Groups**.
2. Click **Create Host Group**.
3. Set the following:
   - **Name**: Descriptive name for the group (e.g., `VMware_RHEL_8`).
   - **Lifecycle Environment**: Select the desired environment.
   - **Content View**: Choose the relevant content view.
   - **Compute Resource**: Select the VMware compute resource.
   - **Compute Profile**: Choose a profile (e.g., CPU, memory, and disk settings).
   - **Operating System**: Assign the OS and partition table.
   - **Provisioning Templates**: Attach the previously created templates.
4. Save the host group.

---

## Step 4: Provision the Virtual Machine

### 4.1 Create a New Host
1. Navigate to **Hosts > Create Host**.
2. Fill in the details:
   - **Host Group**: Select the created host group.
   - **Name**: Provide a unique hostname for the VM.
   - **Compute Resource**: Ensure the correct resource is selected.
   - **Interface**: Configure the primary interface (MAC, subnet, IP).
   - **Volumes**: Adjust disk settings if needed.
3. Save the host configuration.

### 4.2 Start Provisioning
1. Click **Submit** to begin provisioning.
2. Monitor the process in **Hosts > All Hosts**.

---

## Step 5: Post-Provisioning Configuration

### 5.1 Activation Keys
1. Navigate to **Content > Activation Keys**.
2. Assign the activation key to the lifecycle environment and content view.

### 5.2 Additional Configuration
Use **Puppet Classes**, **Ansible Roles**, or custom scripts to finalize configurations post-provisioning.

---

## Troubleshooting

### Common Issues
- **VMware Connection Errors**: Check credentials and vCenter API access.
- **Provisioning Failures**: Validate templates, compute resources, and network configurations.
- **PXE Boot Issues**: Verify TFTP and DHCP services.

### Useful Logs
- Satellite logs:
  ```bash
  /var/log/foreman/production.log
  ```
- VMware errors:
  Check vCenter logs for API or permission issues.

---

## Additional Resources
- [Red Hat Satellite Documentation](https://access.redhat.com/documentation/en-us/red_hat_satellite/)
- [VMware Integration Guide](https://access.redhat.com/articles/6428841)

---
