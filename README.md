# SOP: Virtual Linux Server Setup for Web Application Testing
**Document ID:** SOP-MITT-001 
**Version:** 1.0  
**Owner:** Jun Santos III Sagucio

---

## 1. Purpose and Scope
This Standard Operating Procedure (SOP) defines the steps to provision, configure, and secure a virtualized Linux environment specifically for testing web applications. The goal is to ensure a stable, reproducible, and isolated environment that mimics production settings for accurate functional and security analysis.

## 2. Resource Requirements
Before deployment, allocate the following virtual resources to ensure the testing tools and application run without bottlenecks:

*   **Operating System:** Ubuntu 24.04 LTS (Minimal Install recommended)
*   **vCPU:** 2 Cores (Minimum)
*   **Memory:** 4 GB RAM
*   **Storage:** 30 GB SSD
*   **Networking:** Static IP or DHCP Reservation; Bridged Adapter preferred for external accessibility.
*   **Hostname:** `Mitt-student1`

## 3. Initial OS Setup and Hardening
Follow these steps to initialize the server after the first boot:

1.  **System Update:** Sync repositories and apply the latest security patches.
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
2.  **User Management:** Create a dedicated tester account with sudo privileges.
    ```bash
    sudo adduser qa_tester
    sudo usermod -aG sudo qa_tester
    ```
3.  **Firewall Configuration:** Enable the Uncomplicated Firewall (UFW) to limit the attack surface.
    ```bash
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    sudo ufw allow ssh
    sudo ufw allow http
    sudo ufw allow https
    sudo ufw enable
    ```

## 4. Required Software Stack
A web application test environment requires a mix of hosting services and diagnostic tools. Install the following essential packages:


| Category | Software | Installation Command |
| :--- | :--- | :--- |
| **Web Server** | Nginx | `sudo apt install nginx -y` |
| **Database** | MariaDB | `sudo apt install mariadb-server -y` |
| **Containerization** | Docker | `sudo apt install docker.io -y` |
| **Version Control** | Git | `sudo apt install git -y` |
| **Traffic Analysis** | Curl & Net-tools | `sudo apt install curl net-tools -y` |
| **Security Scan** | OWASP ZAP | `sudo snap install zap --classic` |

## 5. Web Application Test Configuration
Once the environment is live, configure the following for testing readiness:

*   **Log Monitoring:** Set up `tail` or `multitail` to watch `/var/log/nginx/error.log` in real-time.
*   **Environment Variables:** Create a `.env` file in the application root to store test-specific credentials (e.g., `DB_USER=test_user`).
*   **SSL Simulation:** Generate a self-signed certificate for testing HTTPS functionality.
    ```bash
    sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
    ```

## 6. Verification and Snapshotting
Before handing the server over to the QA team, verify the configuration:

1.  **Service Check:** Ensure all critical services are running.
    ```bash
    systemctl status nginx mariadb docker
    ```
2.  **Connectivity:** Confirm the web server responds to the assigned IP from a remote browser.
3.  **Snapshot:** Create a **Virtual Machine Snapshot** (e.g., `Baseline_Config_Ready`) to allow for quick restoration if testing causes system instability.
    2. Export the final test database if required.
    3. Delete the Virtual Machine and all associated snapshots to reclaim hardware resources.
