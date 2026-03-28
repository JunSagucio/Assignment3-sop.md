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

