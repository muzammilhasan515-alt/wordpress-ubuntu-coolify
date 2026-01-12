# How to Deploy WordPress on Ubuntu 24.04 Using Coolify

WordPress can be deployed on an Ubuntu 24.04 server using Coolify, which simplifies application setup and management. Coolify takes care of running the application, connecting it to a database, and setting up secure HTTPS access, so only minimal manual server work is required.

In this setup, WordPress runs together with a MariaDB database on the same server. HTTPS is enabled automatically using Let's Encrypt, allowing the site to be accessed through a custom domain in a secure way and ready for regular use.

---

## Prerequisites

Before starting the installation, make sure the following requirements are in place.

### 1. Server Requirements

- **Operating System:** A fresh installation of Ubuntu 24.04 LTS  
- **Hardware:** Minimum 2 GB RAM and 20 GB storage  
- **Access:** root access (or a user with sudo privileges) via SSH  

> **NOTE**  
> **OS Installation:** If Ubuntu is not yet installed on your server, follow the Official Ubuntu Server Guide before proceeding.

---

### 2. Domain and Networking

- **Domain Name:** A registered domain name (e.g., example.com)  
- **DNS Setup:** An A record pointing your domain or subdomain to the server’s public IP address  

> **IMPORTANT**  
> **DNS Management:** You must configure your DNS records through your domain registrar. This guide assumes your domain already points to your server IP. For help, refer to your registrar's official documentation.

---

### 3. Core Technologies (Official Documentation)

The following software components are used in this deployment. You can refer to their official manuals for advanced configuration:

- Coolify – The open-source platform used for deployment and management  
- WordPress – The Content Management System (CMS)  
- MariaDB – The database engine used to store site content  
- Linux Basics – Familiarity with the terminal and standard commands  

**Estimated Time:** 30–45 minutes

---

## Server Preparation (Ubuntu 24.04)

Before installing Coolify, the Ubuntu server must be updated and configured with a minimal set of required tools. These steps ensure the system is up to date, can securely download external resources, and allow incoming web traffic required for running a WordPress site.

---

## Step 1: Connect to the Server

Open a terminal on your local machine and connect to the server using SSH.

- **On Windows:** You can use PowerShell, Windows Terminal, or Command Prompt (cmd)  
- **On macOS:** Use the built-in Terminal application  
- **On Linux:** Use any standard terminal emulator  

Run the following command, replacing `<your_server_ip>` with your server’s IP address:

```bash
ssh root@<your_server_ip>
