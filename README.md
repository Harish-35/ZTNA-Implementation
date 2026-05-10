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


The Common Name (CN) was set to the Ubuntu server’s private IP address used to access the application.

### 3.4 Configuring Nginx for HTTPS

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

### 3.5 Explanation of Configuration

- Port 80 handles HTTP requests
- HTTP traffic is automatically redirected to HTTPS using:

```bash
return 301 https://$host$request_uri;
```

- Port 443 handles encrypted HTTPS traffic
- SSL certificate and private key paths were defined in the configuration
- Nginx serves the custom ZTNA portal securely over HTTPS


### 3.6 Testing Nginx Configuration

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

### 3.7 Firewall Configuration for HTTPS

HTTPS traffic was allowed through the firewall:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw reload
```

This enabled:

- HTTP (Port 80)
- HTTPS (Port 443)

for internal communication.

### 3.8 HTTPS Validation

The secure web application was accessed through a browser using:

```bash
https://192.168.1.10
```

Validation performed:

- Verified successful HTTPS connection
- Confirmed browser displayed encrypted connection
- Confirmed automatic redirection from HTTP to HTTPS

Since a self-signed certificate was used, the browser displayed a security warning, which was expected in a lab environment.

### 3.9 Security Benefits

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


### 4.2 Purpose of the Custom Portal
The custom web page was created to:
- Simulate an internal enterprise application
- Demonstrate secure application hosting within a private network
- Validate HTTPS communication
- Test Zero Trust access through Twingate

The portal acted as the protected resource within the ZTNA architecture.


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

---

##  5. Twingate Setup

### 5.1 Overview
To implement Zero Trust Network Access (ZTNA), the Twingate platform was configured to securely connect authenticated users to the private Nginx web application hosted on the Ubuntu server.

Twingate was used to:
- Create a Zero Trust access environment
- Securely expose internal resources without public internet exposure
- Establish encrypted outbound tunnels using Twingate Connector
- Enforce identity-based access control

### 5.2 Creating a Twingate Account
A Twingate administrative account was created using the official platform:

```text
https://www.twingate.com
```

After registration:

- A new Twingate tenant/network environment was initialized
- Administrative dashboard access was configured

### 5.3 Creating a Twingate Network

Inside the Twingate Admin Console:

- A new network environment was created for the ZTNA lab

Example:

```bash
ZTNA-Lab-Network
```

This network acted as the centralized Zero Trust environment managing:

- Connectors
- Resources
- User access policies
- Secure tunnels

### 5.4 Configuring the Remote Network

A Remote Network was configured to represent the internal/private Ubuntu environment where the Nginx application was hosted.

Configuration steps:

Navigated to:

```bash
Network → Remote Networks
```

- Created a new Remote Network
- Assigned it to the Ubuntu private network

This allowed Twingate to securely communicate with internal resources.

### 5.5 Deploying the Twingate Connector

The Twingate Connector was deployed inside the Ubuntu environment to establish a secure outbound tunnel between the private network and the Twingate cloud platform.

##### Installing Docker

Docker was installed to run the Twingate Connector container:

```bash
sudo apt update
sudo apt install docker.io -y
```

Docker service was enabled and started:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

### 5.6 Connector Deployment

Inside the Twingate Admin Console:

- Generated Connector deployment tokens
- Selected Docker-based deployment method

The provided Docker command was executed on the Ubuntu server:

```bash
docker run -d \
--name twingate-connector \
--restart unless-stopped \
-v /var/lib/twingate:/var/lib/twingate \
-e TWINGATE_NETWORK="your-network-name" \
-e TWINGATE_ACCESS_TOKEN="your-access-token" \
-e TWINGATE_REFRESH_TOKEN="your-refresh-token" \
twingate/connector:latest
```

### 5.7 Connector Functionality

The Twingate Connector performed the following tasks:

- Established encrypted outbound tunnel to Twingate cloud
- Eliminated need for inbound firewall exposure
- Forwarded authenticated traffic securely to internal resources
- Enabled application-level secure access

The connector operated entirely within the private network.

### 5.8 Verifying Connector Deployment

Docker container status was verified:

```bash
docker ps
```

Successful output confirmed:

- Connector container was running
- Tunnel service was active

Connector status was also verified from the Twingate Admin Dashboard:

- Connector displayed as:

```bash
Connected
```

This confirmed successful communication between:

- Ubuntu private environment
- Twingate cloud platform

### 5.9 Secure Tunnel Validation

Tunnel establishment was validated by:

- Confirming connector heartbeat activity in Twingate dashboard
- Ensuring encrypted outbound communication was active
- Verifying no inbound ports were exposed publicly

Security validation confirmed:

- Tunnel traffic was encrypted using TLS
- Internal resources remained hidden from public internet
- Access could only occur through authenticated Twingate sessions

### 5.10 Security Benefits

The Twingate deployment improved security by:

- Eliminating traditional VPN exposure
- Preventing direct internet access to internal systems
- Enforcing Zero Trust access principles
- Restricting communication to authenticated users only
- Reducing attack surface through outbound-only connectivity


### 5.11 Role in ZTNA Architecture

The Twingate setup formed the core access-control layer of the project.

Architecture flow:

- User authenticates using Twingate Client
- Request is validated through Twingate Cloud
- Traffic is securely tunneled through Twingate Connector
- Connector forwards traffic internally to the Nginx web server

This enabled secure, identity-based access to the private web application without exposing the Ubuntu server to the public internet.

---

##  6. Resource Configuration

### 6.1 Overview
After successfully deploying the Twingate Connector, the private Nginx web server hosted on Ubuntu was configured as a protected resource within the Twingate Zero Trust environment.

This step enabled authenticated users to securely access the internal application through Twingate without exposing the server directly to the public internet.

### 6.2 Purpose of Resource Configuration
The resource configuration process was used to:
- Register the internal Ubuntu-hosted web application within Twingate
- Define which internal service should be protected
- Enable secure application-level access
- Associate the resource with the deployed connector

The private Nginx server became the protected internal resource within the ZTNA architecture.

### 6.3 Identifying the Private Resource
The Ubuntu server hosting the Nginx application was identified using its internal/private IP address.

The IP address was verified using:

```bash
ip addr show
```
Example:

```bash
192.168.1.10
```
This private IP address represented:

- The internal application endpoint
- The protected resource accessible through Twingate

### 6.4 Accessing the Twingate Admin Console

The Twingate Admin Console was used to configure protected resources.

Navigation path:

```bash
Resources → Add Resource
```

This section allows administrators to:

- Define internal applications
- Configure secure access policies
- Associate resources with connectors

### 6.5 Creating the Private Resource

A new resource entry was created for the Ubuntu-hosted Nginx application.

Example configuration:

<p>

Field	                 Value
- **Resource Name**	------>      ZTNA-Web-Portal <br>
- **Address**	      ------>      192.168.1.10    <br>
- **Protocol**	    ------>      HTTPS           <br>
- **Port**	        ------>      443             <br>
</p>
Configuration details:

- Resource Name identifies the internal application
- Address points to the Ubuntu server’s private IP
- HTTPS protocol ensures encrypted communication
- Port 443 corresponds to the secure Nginx service

### 6.6 Mapping Resource to Connector

The configured resource was linked to the previously deployed Twingate Connector.

Connector mapping process:

- Selected the Remote Network associated with the Ubuntu environment
- Assigned the connector responsible for internal traffic forwarding

This mapping ensured:

- Twingate traffic was routed correctly
- Requests reached the internal Nginx server securely
- The application remained accessible only through the connector

### 6.7 Resource Access Workflow

Once configured, the access flow operated as follows:

1. User initiates connection through Twingate Client
2. Twingate Cloud validates user identity and access permissions
3. Secure tunnel traffic is forwarded to the mapped connector
4. Twingate Connector routes traffic internally to:

```bash
https://192.168.1.10
```
5. Nginx server responds with the hosted ZTNA portal page

This process ensured application-level secure access without exposing the server publicly.

### 6.8 Validation & Testing

Resource functionality was validated by:

- Connecting through Twingate Client
- Accessing the private application securely
- Verifying successful communication with the Nginx server

Validation checks included:

- Successful loading of HTTPS web page
- Correct routing through Twingate Connector
- No direct public access to the server

### 6.9 Security Benefits

Resource configuration provided several security advantages:

- Internal applications remained hidden from public internet
- Access was controlled using identity-based policies
- No inbound ports required public exposure
- Communication occurred through encrypted tunnels only
- Reduced attack surface compared to traditional VPN solutions

### 6.10 Role in Zero Trust Architecture

The resource configuration stage connected the private web application to the Zero Trust access framework.

This completed the secure application exposure process by:

- Defining the protected application
- Linking the application to the secure connector
- Enabling authenticated user access through Twingate

The Ubuntu-hosted Nginx server was now securely accessible as a protected Zero Trust resource.4

---

##  7. Access Control & Testing

### 7.1 Overview
After configuring the protected resource within Twingate, user access control and connectivity testing were performed to validate the Zero Trust Network Access (ZTNA) implementation.

This phase ensured:
- Only authenticated users could access the application
- Secure communication was functioning correctly
- Direct public access to the internal server remained blocked


### 7.2 Purpose of Access Control
The access control configuration was implemented to:
- Enforce identity-based authentication
- Restrict application access to authorized users only
- Prevent unauthorized access to internal resources
- Validate Zero Trust security principles

Unlike traditional VPN solutions, access was limited specifically to the protected application rather than the entire network.


### 7.3 Configuring User Access in Twingate
User access policies were configured through the Twingate Admin Console.

Navigation path:
```text
 id="0bmj6y"
Admin Console → Teams / Users
```

Configuration steps:

- Added authorized user accounts
- Assigned users to access groups
- Linked the protected resource to the permitted user group

Example:

```text
User Group: ZTNA-Users
Allowed Resource: ZTNA-Web-Portal
```

This ensured only approved users could access the internal application.

7.4 Installing Twingate Client

The Twingate Client application was installed on the user endpoint system to establish secure access to the protected resource.

Downloaded from:

```text
https://www.twingate.com/download
```
Supported platforms:

- Windows
- Linux
- macOS

After installation:

- User authenticated using registered credentials
- Client established secure communication with Twingate Cloud

### 7.5 User Authentication Process

The access workflow operated as follows:

1. User launched Twingate Client
2. User authenticated using assigned identity credentials
3. Twingate validated:
  - User identity
  - Assigned access permissions
  - Resource authorization policies
4. Upon successful validation, secure access was granted to the protected application

This enforced Zero Trust principles by verifying every access request before allowing connectivity.

### 7.6 Secure Application Access Testing

The protected application was accessed using the Ubuntu server’s private HTTPS address:

```bash
https://192.168.1.10
```

Validation steps:

- Confirmed successful loading of the custom ZTNA portal page
- Verified HTTPS-secured communication
- Verified traffic routing through Twingate Connector
- Confirmed authenticated access via Twingate Client

This demonstrated successful Zero Trust-based application access.

### 7.7 Direct Access Restriction Testing

Additional testing was performed to ensure the application was not directly accessible without Twingate authentication.

Validation performed:

- Attempted access without Twingate Client connection
- Attempted access from unauthorized devices/users
- Verified restricted communication outside the secure tunnel

Results confirmed:

- Direct public access was blocked
- Application remained inaccessible to unauthenticated users
- Internal resource exposure was successfully prevented

### 7.8 Tunnel & Connectivity Validation

Secure tunnel functionality was validated by:

- Confirming active Twingate Client session
- Verifying connector activity within Twingate dashboard
- Ensuring encrypted communication between:
  - Client
  - Twingate Cloud
  - Connector
  - Nginx server

This validated end-to-end secure application delivery.

### 7.9 Security Validation Results

Security testing confirmed:

- Identity-based access enforcement
- Secure HTTPS communication
- Private resource isolation
- No direct public exposure of Ubuntu server
- Reduced attack surface compared to traditional VPN-based access

The implementation successfully aligned with Zero Trust principles:

```bash
Never Trust, Always Verify
```

### 7.10 Final Outcome

The final ZTNA environment successfully demonstrated:

- Secure remote application access
- Identity-driven authorization
- Encrypted communication through HTTPS and TLS tunnels
- Protection of private infrastructure without public exposure

The Ubuntu-hosted Nginx application was now securely accessible only to authenticated users through the Twingate Zero Trust architecture.
