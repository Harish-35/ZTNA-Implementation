# 🛡️ ZTNA-Implementation
Designed and implemented a Zero Trust Network Access (ZTNA) architecture using Twingate, securing a private Nginx web application on Ubuntu with HTTPS encryption and controlled access via identity-based authentication, eliminating the need for traditional VPN.

---

##  Architecture
- **Ubuntu Server** – Hosts the private web application  
- **Nginx** – Web server configured with HTTPS  
- **Twingate Connector** – Secure outbound tunnel from private network  
- **Twingate Cloud** – Identity-based access control  
- **Client Device** – Access via Twingate client application

  <img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/1c22ac50-11e3-4cdb-a86b-eacce3b1a6e8" />

<img width="500" height="300" alt="1" src="https://github.com/user-attachments/assets/1f5df94d-2a13-440c-b7b3-0045752ade4d" />


---

##  Technologies Used
- Ubuntu Server  
- Nginx  
- OpenSSL (Self-Signed Certificate)  
- Twingate (ZTNA Platform)  
- Networking (Private IP addressing)
