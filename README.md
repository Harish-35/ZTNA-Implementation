# 🛡️ ZTNA-Implementation
Designed and implemented a Zero Trust Network Access (ZTNA) architecture using Twingate, securing a private Nginx web application on Ubuntu with HTTPS encryption and controlled access via identity-based authentication, eliminating the need for traditional VPN.

---

##  Architecture
- **Client Device** – Access via Twingate client application
- **Twingate Cloud** – Identity-based access control
- **Ubuntu Server** – Hosts the private web application
- **Twingate Connector** – Secure outbound tunnel from private network
- **Nginx** – Web server configured with HTTPS


  <img width="1000" height="540" alt="1" src="https://github.com/user-attachments/assets/1f5df94d-2a13-440c-b7b3-0045752ade4d" />


---

##  Technologies Used
  
- Ubuntu Server  
- Nginx  
- OpenSSL (Self-Signed SSL Certificate)  
- Twingate (Zero Trust Network Access Platform)  
- Twingate Connector  
- Twingate Client  
- HTTPS / TLS  
- Networking (Private IP Addressing)

---

##  Implementation Steps

### 1. Ubuntu Server Setup

#### 1.1 Virtual Environment Setup
- Installed VirtualBox on host machine to create an isolated lab environment  
- Created a new Virtual Machine with the following configuration:
  - OS: Ubuntu (64-bit)
  - RAM: 2 GB (minimum)
  - Storage: 20 GB (dynamically allocated)
  - Network Adapter: NAT / Bridged Adapter (for internet + local access)

#### 1.2 Ubuntu Server Installation
- Downloaded Ubuntu Server ISO from official website  
- Mounted ISO in VirtualBox and started the VM  
- Completed installation with:
  - Username and password setup  
  - Enabled OpenSSH (optional for remote access)  
- Successfully booted into Ubuntu terminal after installation  

#### 1.3 System Update & Upgrade
Updated package list and installed latest security updates:

  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

- Ensured system is up-to-date before installing any services

#### 1.4 Network Connectivity Testing

Verified network interface details:

```bash
ip addr show
```
- Identified private IP address (e.g., 192.168.x.x or 10.x.x.x)
- Ensured the VM is part of a private/internal network
- Used Bridged Adapter (or NAT with port forwarding) depending on setup

 #### 1.5 Network Connectivity Testing

Verified internet connectivity:

```bash
ping 8.8.8.8
```

Verified DNS resolution:

```bash
ping google.com
```

#### 1.6 Security Considerations
- Ensured the Ubuntu server is not exposed to the public internet
- Restricted access to internal/private network only

(Optional) Configured firewall using UFW:

```bash
sudo ufw enable
```
```bash
sudo ufw allow OpenSSH
```

#### 1.7 Final Validation
- Confirmed system readiness for hosting applications
- Verified stable network, updated packages, and secure configuration



##  2. Nginx Installation & Configuration

### 2.1 Installing Nginx Web Server
After completing the Ubuntu server setup, Nginx was installed to host the internal web application used in the ZTNA environment.

The package repository was updated before installation to ensure the latest stable packages were used:

```bash
sudo apt update
```

Nginx was then installed using the APT package manager:

```bash
sudo apt install nginx -y
```

The -y flag automatically confirms the installation process.


### 2.2 Verifying Nginx Service Status

Once the installation was completed, the Nginx service status was checked to confirm that the web server was running correctly.

```bash
sudo systemctl status nginx
```
The output confirmed:

- Nginx service was active and running
- No startup or configuration errors were present

To ensure the service automatically starts during every system boot, the following command was executed:

```bash
sudo systemctl enable nginx
```


### 2.3 Identifying the Server IP Address

The Ubuntu server’s private IP address was identified to allow access to the hosted web application from devices inside the internal network.

```bash
ip addr show
```

Example private IP:

```bash
192.168.1.10
```

This private IP address was later used for:

- Internal web access
- Twingate resource configuration
- HTTPS testing


### 2.4 Testing Default Nginx Web Page

After installation, the default Nginx welcome page was tested from a browser using the Ubuntu server’s private IP address.

```bash
http://192.168.1.10
```

Successful loading of the page confirmed:
- Nginx was functioning correctly
- Internal network communication was working properly
- The web service was accessible within the private environment


### 2.5 Creating a Custom ZTNA Web Page

The default Nginx page was replaced with a custom portal page for the project.

Navigated to the default web root directory:

```bash
cd /var/www/html
```

Edited the default landing page:

```bash
sudo nano index.nginx-debian.html
```

Custom HTML content was added to simulate a secure ZTNA portal interface.

Example:

```bash
<h1>Welcome to ZTNA Secure Portal</h1>
<p>Access protected through Zero Trust Architecture</p>

```

This page acted as the private application protected by the ZTNA platform.


## 2.6 Configuring Firewall Rules

Firewall rules were configured to allow HTTP traffic for internal communication.

```bash
sudo ufw allow 'Nginx HTTP'
sudo ufw reload
```

This ensured:
- Internal devices could access the web application
- Only required traffic was permitted
- The environment remained controlled and secure


### 2.7 Restarting and Validating Nginx
After all modifications, the Nginx service was restarted to apply configuration changes.

```bash
sudo systemctl restart nginx
```

Validation steps performed:
- Accessed the custom web page from another device inside the network
- Verified successful response from the server
- Confirmed the application was hosted correctly on Ubuntu


### 2.8 Security Considerations
The Nginx server was intentionally hosted within a private/internal network and was not directly exposed to the public internet.

Key security measures:
- Private IP-based access only
- No public inbound exposure
- Prepared for HTTPS encryption and ZTNA integration
- Reduced attack surface before external access configuration

This setup formed the foundation for implementing secure Zero Trust access using Twingate in later stages of the project.
