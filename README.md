# 🛡️ ZTNA-Implementation
Designed and implemented a Zero Trust Network Access (ZTNA) architecture using Twingate, securing a private Nginx web application on Ubuntu with HTTPS encryption and controlled access via identity-based authentication, eliminating the need for traditional VPN.

---

## 🏗️ Architecture
- **Ubuntu Server** – Hosts the private web application  
- **Nginx** – Web server configured with HTTPS  
- **Twingate Connector** – Secure outbound tunnel from private network  
- **Twingate Cloud** – Identity-based access control  
- **Client Device** – Access via Twingate client application  

---

## ⚙️ Technologies Used
- Ubuntu Server  
- Nginx  
- OpenSSL (Self-Signed Certificate)  
- Twingate (ZTNA Platform)  
- Networking (Private IP addressing)
