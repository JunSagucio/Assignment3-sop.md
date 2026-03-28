# Assignment3-sop.md
SOP Documentation
# Standard Operating Procedure: Virtual Linux Server Setup
**Title:** Provisioning a Linux Environment for Web Application Testing  
**Document ID:** SOP-MITT-001  
**Version:** 1.0  
**Status:** Active  

---

## 1. Purpose
This document defines the standardized process for creating and configuring a Virtual Linux Server (VM) specifically optimized for executing web application functional, performance, and security tests.

## 2. Infrastructure Specifications
Before beginning the installation, ensure the hypervisor (VirtualBox, VMware, or Cloud Provider) is configured with the following minimum resources:


| Resource | Requirement |
| :--- | :--- |
| **Operating System** | Ubuntu 22.04 LTS |
| **CPU** | 2 vCPUs |
| **Memory (RAM)** | 4 GB |
| **Storage** | 40 GB SSD (Thin Provisioned) |
| **Network Mode** | Bridged Adapter (for unique LAN IP) |
| **Hostname** | `MITT test-server` |

---

## 3. Step-by-Step Installation Procedure

### Phase 1: Initial OS Setup
1. **Boot from ISO:** Load the Ubuntu 22.04 Server ISO into the virtual drive.
2. **User Configuration:** Create a primary administrative user:
   - **Username:** `test-admin`
   - **Password:** `testpassword`
3. **Network Configuration:** Set a static IP address if possible, or ensure DHCP is active.
4. **SSH Access:** Select "Install OpenSSH Server" during the setup wizard to allow remote terminal access.

### Phase 2: System Update & Hardening
Once the OS is installed and you have logged in, run the following commands to ensure the system is secure and up-to-date:
```bash
# Update package lists and upgrade existing software
sudo apt update && sudo apt upgrade -y

# Configure basic firewall rules
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable

---

###  Phase 3: Software Stack & Testing Tools
This phase installs the core environment and the specialized tools required for comprehensive web application probing.

### A. Web Server & Runtime Environment
Install the primary web server (Nginx) and common language runtimes used by modern testing frameworks.
```bash
# Install Nginx and language runtimes
sudo apt install nginx nodejs npm python3-pip -y

# Verify installations
nginx -v && node -v && python3 --version

---

###  Phase 4: Environment Verification Checklist
Before handing the server over to the testing team, verify the configuration against the following criteria to ensure the environment is stable and correctly provisioned.


| Category | Verification Command | Expected Result |
| :--- | :--- | :--- |
| **Network** | `curl -I http://localhost` | HTTP/1.1 200 OK |
| **Security** | `sudo ufw status` | Status: active (Ports 22, 80, 443 allowed) |
| **Runtime** | `node -v && python3 --version` | Version numbers displayed without errors |
| **Storage** | `df -h /` | At least 20GB of free space remaining |
| **Services** | `systemctl is-active nginx` | `active` |

### Post-Installation Validation Steps
1. **External Access:** Open a browser on a secondary machine and navigate to the VM's IP address. The "Welcome to nginx!" page must be visible.
2. **Permission Check:** Ensure the `test-admin` user can execute `sudo` commands without being prompted for a root password (if configured) or that the password is functional.
3. **Tool Accessibility:** Run `jq --version` and `curl --version` to ensure CLI testing utilities are in the system PATH.

---

### Phase 5: Maintenance & Operational Management
To maintain the integrity of web application tests, the server state must be managed strictly to prevent "configuration drift."

### A. Environment Reset (Snapshot Management)
* **Creation:** Immediately following the successful completion of Phase 4, create a VM Snapshot named `GOLDEN_IMAGE_V1`.
* **Restoration:** Revert to the `GOLDEN_IMAGE_V1` snapshot after every destructive test or at the start of a new testing sprint to ensure a "clean-room" environment.

### B. Routine Updates
* **Security Patches:** Run `sudo apt update && sudo apt upgrade -y` monthly.
* **Dependency Refresh:** Update Node.js and Python packages (`npm update` and `pip install --upgrade`) only when a specific test suite requires a newer version.

### C. Logging and Artifacts
* **Log Rotation:** Ensure `logrotate` is active for Nginx to prevent the 40 GB SSD from filling up with request logs.
* **Test Artifacts:** Move all test reports (HTML, JSON, XML) to a centralized storage or GitHub repository; do not store them permanently on the test server.

### D. Decommissioning
* When the web application project concludes:
    1. Backup the custom configuration files in `/etc/nginx/sites-available/`.
    2. Export the final test database if required.
    3. Delete the Virtual Machine and all associated snapshots to reclaim hardware resources.
