# Installing a Red Hat Satellite Capsule Server

This guide provides detailed steps to install and configure a Red Hat Satellite Capsule Server. A Capsule Server acts as a proxy for content delivery and configuration management for Red Hat Satellite.

---

## Prerequisites

### 1. System Requirements
Ensure the server meets the following minimum specifications:
- **CPU**: 4 cores
- **RAM**: 16 GB
- **Disk Space**: At least 50 GB for `/var` (additional storage for content caching is recommended).
- **Operating System**: RHEL 8.x or later.

### 2. Network Configuration
- **Hostname**: Fully Qualified Domain Name (FQDN) must be resolvable.
- **DNS**: Ensure forward and reverse DNS resolution is configured.
- **Ports**: Open the required ports for communication (e.g., 80, 443, 5646, 5647).
- **Firewall**: Allow necessary traffic using `firewalld` or your preferred firewall manager.

### 3. Red Hat Subscription
- Ensure the system is registered with Red Hat Subscription Manager.
- Attach the required Satellite Capsule subscription.

### 4. Red Hat Satellite Configuration
- You must have a running Red Hat Satellite server.
- Configure lifecycle environments and content views in Satellite before proceeding.

---

## Step 1: Register the Server

### 1.1 Register with Red Hat Subscription Manager
```bash
sudo subscription-manager register --username <YOUR_RH_USERNAME> --password <YOUR_RH_PASSWORD>
```

### 1.2 Attach the Capsule Subscription
List available subscriptions:
```bash
sudo subscription-manager list --available
```
Attach the appropriate subscription:
```bash
sudo subscription-manager attach --pool=<POOL_ID>
```

### 1.3 Enable Required Repositories
Enable the necessary repositories:
```bash
sudo subscription-manager repos --enable=rhel-8-for-x86_64-baseos-rpms \
                                 --enable=rhel-8-for-x86_64-appstream-rpms \
                                 --enable=rhel-8-for-x86_64-satellite-capsule-7-rpms
```

Update the system:
```bash
sudo dnf update -y
```

---

## Step 2: Install the Capsule Server Package

### 2.1 Install Capsule Packages
Install the Capsule server:
```bash
sudo dnf install -y satellite-capsule
```

### 2.2 Install Additional Packages (Optional)
If additional services are required (e.g., DHCP, DNS, TFTP):
```bash
sudo dnf install -y satellite-capsule-dhcp satellite-capsule-dns satellite-capsule-tftp
```

---

## Step 3: Configure the Capsule Server

### 3.1 Initial Configuration
Run the Capsule installer:
```bash
sudo capsule-installer \
  --foreman-proxy-register-in-foreman true \
  --foreman-proxy-foreman-base-url "https://<SATELLITE_FQDN>" \
  --foreman-proxy-trusted-hosts "<SATELLITE_FQDN>" \
  --foreman-proxy-trusted-hosts "<CAPSULE_FQDN>" \
  --foreman-proxy-oauth-consumer-key "<OAUTH_KEY>" \
  --foreman-proxy-oauth-consumer-secret "<OAUTH_SECRET>" \
  --puppet true \
  --puppetca true \
  --content true \
  --pulp true \
  --certs-tar-file "~/capsule-certs.tar"
```

### 3.2 Generate Capsule Certificates
On the Satellite server:
```bash
satellite-installer --scenario satellite \
  --capsule-fqdn "<CAPSULE_FQDN>" \
  --certs-tar-file "~/capsule-certs.tar"
```
Transfer the `capsule-certs.tar` file to the Capsule server.

---

## Step 4: Verify Installation

### 4.1 Verify Services
Ensure all services are running:
```bash
sudo systemctl status
```

### 4.2 Verify Registration
Log in to the Satellite web interface and confirm that the Capsule server is registered and synchronized.

---

## Step 5: Synchronize Content

### 5.1 Enable Content Sync
Enable content sync for the Capsule server in the Satellite web interface:
1. Navigate to **Infrastructure > Capsules**.
2. Select the Capsule server.
3. Enable desired content views and lifecycle environments.

### 5.2 Trigger Synchronization
Manually synchronize the content:
```bash
sudo foreman-maintain content sync
```

---

## Troubleshooting

### Common Issues
- **Certificate Errors**: Verify the FQDN and ensure the correct `capsule-certs.tar` file was used.
- **Service Failures**: Check logs under `/var/log/foreman-proxy` or `/var/log/messages`.

### Useful Commands
- Check Capsule status:
  ```bash
  sudo foreman-maintain health check
  ```
- Restart services:
  ```bash
  sudo systemctl restart foreman-proxy
  ```

---

## Additional Resources
- [Red Hat Satellite Documentation](https://access.redhat.com/documentation/en-us/red_hat_satellite/)
- [Red Hat Knowledgebase](https://access.redhat.com/knowledge/)

---
