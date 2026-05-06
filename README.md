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
- Updated package list and installed latest security updates:
  ```bash


  
  sudo apt update && sudo apt upgrade -y
