# Installation Guide for Red Hat Satellite

Red Hat Satellite allows for the centralized management of Linux servers, using automation to help users and system administrators provision, configure and update their systems more quickly and easily.

## INSTALLATION

### Register the System
```bash
subscription-manager register
```
- **Username:** `user_name`  
- **Password:** (enter your password)

Example output:
```
The system has been registered with ID: 541084ff2-44cab-4eb1-9fa1-7683431bcf9a
```

### Identify the Pool ID of the Satellite Infrastructure Subscription
Run the following command:
```bash
subscription-manager list --all --available --matches 'Red Hat Satellite Infrastructure Subscription'
```

Example output:
```
Subscription Name:   Red Hat Satellite Infrastructure Subscription
Provides:            Red Hat Satellite
                     Red Hat Software Collections (for RHEL Server)
                     Red Hat CodeReady Linux Builder for x86_64
                     Red Hat Ansible Engine
                     Red Hat Enterprise Linux Load Balancer (for RHEL Server)
                     Red Hat Enterprise Linux Server
                     Red Hat Satellite Capsule
                     Red Hat Enterprise Linux for x86_64
                     Red Hat Enterprise Linux High Availability for x86_64
                     Red Hat Satellite 5 Managed DB
                     Red Hat Satellite 6
                     Red Hat Discovery
SKU:                 MCT3719
Contract:            11878983
Pool ID:             8a85f99968b92c3701694ee998cf03b8
Provides Management: No
Available:           1
Suggested:           1
Service Level:       Premium
Service Type:        L1-L3
Subscription Type:   Standard
Ends:                03/04/2020
System Type:         Physical
```

### Attach the Subscription
Attach the subscription using the `Pool ID`:
```bash
subscription-manager attach --pool=8a85f99968b92c3701694ee998cf03b8
```

Verify the attached subscription:
```bash
subscription-manager list --consumed
```

The Red Hat Satellite Infrastructure subscription provides access to:
- Red Hat Satellite
- Red Hat Enterprise Linux
- Red Hat Software Collections (RHSCL) content

---

## REPO CONFIGURATION

### Disable All Repositories
```bash
subscription-manager repos --disable "*"
```

### Enable Required Repositories
```bash
subscription-manager repos --enable=rhel-7-server-rpms \
--enable=rhel-7-server-satellite-6.7-rpms \
--enable=rhel-7-server-satellite-maintenance-6-rpms \
--enable=rhel-server-rhscl-7-rpms \
--enable=rhel-7-server-ansible-2.8-rpms
```

### Clean and List Repositories
```bash
yum clean all
yum repolist enabled
```

---

## INSTALLING SATELLITE SERVER

### Update and Install Satellite
```bash
yum update
yum install satellite
```

### Run Satellite Installer
```bash
satellite-installer --scenario satellite \
--foreman-initial-admin-username admin \
--foreman-initial-admin-password redhat \
--foreman-proxy-puppetca true \
--foreman-proxy-tftp true \
--enable-foreman-plugin-discovery
```

---

This guide walks through registering the system, attaching the subscription, configuring repositories, and installing Red Hat Satellite.
