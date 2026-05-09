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

### 1.1 Virtual Environment Setup
- Installed VirtualBox on host machine to create an isolated lab environment  
- Created a new Virtual Machine with the following configuration:
  - OS: Ubuntu (64-bit)
  - RAM: 2 GB (minimum)
  - Storage: 20 GB (dynamically allocated)
  - Network Adapter: NAT / Bridged Adapter (for internet + local access)

### 1.2 Ubuntu Server Installation
- Downloaded Ubuntu Server ISO from official website  
- Mounted ISO in VirtualBox and started the VM  
- Completed installation with:
  - Username and password setup  
  - Enabled OpenSSH (optional for remote access)  
- Successfully booted into Ubuntu terminal after installation  

### 1.3 System Update & Upgrade
Updated package list and installed latest security updates:

  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

- Ensured system is up-to-date before installing any services

### 1.4 Network Connectivity Testing

Verified network interface details:

```bash
ip addr show
```
- Identified private IP address (e.g., 192.168.x.x or 10.x.x.x)
- Ensured the VM is part of a private/internal network
- Used Bridged Adapter (or NAT with port forwarding) depending on setup

 ### 1.5 Network Connectivity Testing

Verified internet connectivity:

```bash
ping 8.8.8.8
```

Verified DNS resolution:

```bash
ping google.com
```

### 1.6 Security Considerations
- Ensured the Ubuntu server is not exposed to the public internet
- Restricted access to internal/private network only

(Optional) Configured firewall using UFW:

```bash
sudo ufw enable
```
```bash
sudo ufw allow OpenSSH
```

### 1.7 Final Validation
- Confirmed system readiness for hosting applications
- Verified stable network, updated packages, and secure configuration

---

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


### 2.6 Configuring Firewall Rules

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

---

##  3. HTTPS Enablement

### 3.1 Overview
To secure communication between the client and the hosted web application, HTTPS was enabled on the Nginx web server. SSL/TLS encryption was implemented using a self-signed certificate generated through OpenSSL.

This ensured that all traffic between the user and the server was encrypted, aligning with Zero Trust security principles.


### 3.2 Installing OpenSSL
OpenSSL was used to generate the SSL certificate and private key required for HTTPS configuration.

Verified OpenSSL installation:

```bash
openssl version
```

If not installed, it was installed using:
 
 ```bash
sudo apt install openssl -y
```

### 3.3 Creating SSL Certificate and Private Key

Directories for storing SSL files were created:
```bash
sudo mkdir -p /etc/ssl/private

sudo mkdir -p /etc/ssl/certs
```

A self-signed SSL certificate and RSA private key were generated using the following command:

 ```bash
sudo openssl req -x509 -nodes -days 365 \

-newkey rsa:2048 \
-keyout /etc/ssl/private/nginx-selfsigned.key \
-out /etc/ssl/certs/nginx-selfsigned.crt
```

Command Breakdown
- -x509 → Generates a self-signed certificate
- -nodes → Prevents password encryption on the private key
- -days 365 → Certificate validity period (1 year)
- rsa:2048 → Generates a 2048-bit RSA key
- -keyout → Stores the private key
- -out → Stores the SSL certificate


### 3.4 Certificate Information Configuration

During certificate generation, certificate details were configured:

Example:

```bash
Country Name: IN
State: Tamil Nadu
Locality: Chennai
Organization Name: ZTNA Lab
Common Name: 192.168.1.10
```

The Common Name (CN) was set to the Ubuntu server’s private IP address used to access the application.

### 3.5 Configuring Nginx for HTTPS

The Nginx configuration file was modified to enable SSL/TLS encryption and enforce HTTPS-only access.

Opened the Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/default
```

Added HTTPS server configuration:

```bash
server {
    listen 80;
    server_name _;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name _;

    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;

    root /var/www/html;
    index index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 3.6 Explanation of Configuration

- Port 80 handles HTTP requests
- HTTP traffic is automatically redirected to HTTPS using:

```bash
return 301 https://$host$request_uri;
```

- Port 443 handles encrypted HTTPS traffic
- SSL certificate and private key paths were defined in the configuration
- Nginx serves the custom ZTNA portal securely over HTTPS


### 3.7 Testing Nginx Configuration

The Nginx configuration syntax was validated before restarting the service:

```bash
sudo nginx -t
```

Successful output:

```bash
syntax is ok
test is successful
```

Nginx was then restarted:

```bbash
sudo systemctl restart nginx
```

### 3.8 Firewall Configuration for HTTPS

HTTPS traffic was allowed through the firewall:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw reload
```

This enabled:

- HTTP (Port 80)
- HTTPS (Port 443)

for internal communication.

### 3.9 HTTPS Validation

The secure web application was accessed through a browser using:

```bash
https://192.168.1.10
```

Validation performed:

- Verified successful HTTPS connection
- Confirmed browser displayed encrypted connection
- Confirmed automatic redirection from HTTP to HTTPS

Since a self-signed certificate was used, the browser displayed a security warning, which was expected in a lab environment.

### 3.10 Security Benefits

Enabling HTTPS provided the following security improvements:

- Encrypted communication between client and server
- Protection against packet sniffing and man-in-the-middle attacks
- Secure delivery of the hosted web application
- Improved alignment with Zero Trust security principles

This HTTPS-enabled Nginx server later became the protected private resource within the Twingate ZTNA architecture.

---

##  4. Custom Web Application

### 4.1 Overview
After configuring the Nginx web server and enabling HTTPS, a custom web application page was developed to simulate a secure internal portal protected by the Zero Trust Network Access (ZTNA) environment.

The application was hosted locally on the Ubuntu-based Nginx server and served as the private resource accessed through Twingate.

---

### 4.2 Purpose of the Custom Portal
The custom web page was created to:
- Simulate an internal enterprise application
- Demonstrate secure application hosting within a private network
- Validate HTTPS communication
- Test Zero Trust access through Twingate

The portal acted as the protected resource within the ZTNA architecture.

---

### 4.3 Navigating to the Web Root Directory
The default Nginx web root directory was accessed:

```bash
cd /var/www/html
```

This directory stores the files served by the Nginx web server.

### 4.4 Modifying the Default Web Page

The default Nginx landing page was edited using Nano text editor:

```bash
sudo nano index.nginx-debian.html
```

The default content was replaced with a custom HTML-based portal page.

### 4.5 Developing the Custom Portal Page

A simple HTML application was created to represent a secure ZTNA portal.

Example implementation:

```bash

<!DOCTYPE html>
<html>
<head>
    <title>ZTNA Secure Portal</title>
    <style>
        body {
            background-color: #0d1117;
            color: #00ffcc;
            font-family: Arial, sans-serif;
            text-align: center;
            padding-top: 10%;
        }

        h1 {
            font-size: 40px;
        }

        p {
            font-size: 20px;
        }
    </style>
</head>
<body>

    <h1>Welcome to ZTNA Secure Portal</h1>
    <p>Protected through Zero Trust Network Access</p>

</body>
</html>
```

### 4.6 Application Features

The custom portal included:

- Simple user-friendly interface
- HTTPS-secured web access
- Internal hosting within private network
- Simulation of enterprise-style secure application access

The application was intentionally lightweight to focus on the ZTNA architecture rather than application complexity.

### 4.7 Deploying the Application

After saving the HTML file, the application was automatically hosted through Nginx since the file was placed inside the web root directory.

The Nginx service was restarted to ensure changes were applied:

```bash
sudo systemctl restart nginx
```

### 4.8 Accessing the Web Application

The hosted portal was accessed using the Ubuntu server’s private IP address:

```bash
https://192.168.1.10
```

Validation performed:

- Confirmed successful loading of the custom page
- Verified HTTPS encryption
- Verified accessibility only within the internal network


### 4.9 Security Considerations

The custom application was hosted in a controlled internal environment with the following security measures:

- HTTPS-only communication enabled
- No direct public exposure
- Access restricted to private network
- Prepared for identity-based access through Twingate

This ensured the web application aligned with Zero Trust principles before integrating it into the Twingate ZTNA architecture.

### 4.10 Role in ZTNA Architecture

The custom web application functioned as the protected internal resource within the Zero Trust environment.

In later stages:

- Twingate Connector securely exposed the application
- Only authenticated users could access the portal
- Direct public access remained blocked

This completed the application layer of the ZTNA implementation.
