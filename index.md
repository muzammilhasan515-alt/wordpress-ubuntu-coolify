# How to Deploy WordPress on Ubuntu 24.04 Using Coolify

WordPress can be deployed on an **Ubuntu 24.04** server using **Coolify**, which simplifies application setup and management. Coolify takes care of running the application, connecting it to a database, and setting up secure HTTPS access, so only minimal manual server work is required.

In this setup, **WordPress** runs together with a **MariaDB** database on the same server. HTTPS is enabled automatically using **Let's Encrypt**, allowing the site to be accessed through a custom domain in a secure way and ready for regular use.

## Prerequisites

Before starting the installation, make sure the following requirements are in place.

### 1. Server Requirements

- **Operating System:** A fresh installation of [Ubuntu 24.04 LTS](https://ubuntu.com/tutorials/install-ubuntu-server)  
- **Hardware:** Minimum 2 GB RAM and 20 GB storage  
- **Access:** Root access (or a user with sudo privileges) via SSH  

!!! NOTE If Ubuntu is not yet installed on your server, follow the [Official Ubuntu Server Guide](https://ubuntu.com/tutorials/install-ubuntu-server) before proceeding.

### 2. Domain and Networking

- **Domain Name:** A registered domain name (e.g., example.com)  
- **DNS Setup:** An A record pointing your domain or subdomain to the server’s public IP address  

!!! Note DNS Management: You must configure your DNS records through your domain registrar. This guide assumes your domain already points to your server IP. For help, refer to your registrar's official documentation.

### 3. Core Technologies (Official Documentation)

The following software components are used in this deployment. You can refer to their official manuals for advanced configuration:

- [Coolify](https://coolify.io/docs) – The open-source platform used for deployment and management  
- [WordPress](https://wordpress.org/documentation/) – The Content Management System (CMS)  
- [MariaDB](https://mariadb.com/kb/en/documentation/) – The database engine used to store site content  
- [Linux Basics](https://ubuntu.com/tutorials/command-line-for-beginners) – Familiarity with the terminal and standard commands  

**Estimated Time:** 30–45 minutes

## Server Preparation (Ubuntu 24.04)

Before installing Coolify, the Ubuntu server must be updated and configured with a minimal set of required tools. These steps ensure the system is up to date, can securely download external resources, and allow incoming web traffic required for running a WordPress site.

### Step 1: Connect to the Server

Open a terminal on your local machine and connect to the server using SSH.

- **Windows:** PowerShell, Windows Terminal, or Command Prompt  
- **macOS:** Terminal  
- **Linux:** Any standard terminal emulator  

Run the following command, replacing `<your_server_ip>` with your server’s IP address:

```bash
ssh root@<your_server_ip>
```

Example: `ssh root@192.0.2.1`

![](/images/image16.png)

When prompted, enter the password provided for the server. You will see a prompt similar to the following:

```bash
root@SERVER_IP's password:
```
![](/images/image25.png)

!!! note  You won't see any characters appear as you type - this is normal for security reasons.

After entering the password and pressing **Enter**, the server displays a welcome message confirming that Ubuntu 24.04 is running, followed by a command prompt similar to the one shown below:

```bash
Welcome to Ubuntu 24.04 LTS (GNU/Linux x86_64)
...
root@SERVER_HOSTNAME:~#
```
The prompt ending with the `#` symbol indicates that you are logged in as the root user. This confirms that the SSH connection was successful and that you can proceed with the server preparation steps.

![](/images/image4.png)

### Step 2: Update the System

Before installing additional software, update the server to ensure the latest security patches and system updates are applied.
Run the following command:

```bash
apt update && apt upgrade -y
```

This command performs the following actions:

- `apt update` – Retrieves the latest list of available package updates

- `apt upgrade -y` – Installs all available updates automatically

During the update process, Ubuntu may display output showing packages being downloaded, unpacked, and configured. This is expected behavior.

Once the update completes successfully, the terminal returns to the root prompt, as shown below:

![](/images/image31.png)

### Step 3: Install Required System Packages

Install the basic system utilities required to download and securely install Coolify:

```bash
apt install -y curl ca-certificates ufw
```

These packages provide essential functionality for the remaining steps in this guide:

- `curl` is used to download the Coolify installation script from a remote URL.

- `ca-certificates` ensures the system can verify and trust HTTPS connections when downloading external resources.

- `ufw` (Uncomplicated Firewall) is used to manage basic firewall rules and control incoming network traffic.

Once the command completes successfully, the required system packages are available, and the server is ready for firewall configuration.

![](/images/image30.png)

### Step 4: Configure the Firewall

To secure the server and allow access to the WordPress site, configure the firewall to permit only the required network traffic.

Allow **SSH** (Secure Shell)  access so you can continue managing the server remotely:

```bash
ufw allow OpenSSH
```

Allow **HTTP traffic on port 80**, which is required for initial web access and SSL certificate issuance:

```bash
ufw allow 80
```

Allow **HTTPS traffic on port 443**, which is required for secure access to the WordPress site:

```bash
ufw allow 443
```

Enable the **firewall**:

```bash
ufw enable
```
You'll see a warning:

```
Command may disrupt existing ssh connections. Proceed with operation (y|n)?
```

Type **`y`** and press Enter.

Verify the **firewall status** and active rules:

```bash
ufw status
```

You should see rules allowing **OpenSSH**, **80**, and **443**.

![](/images/image28.png)

This reduces the server’s exposure to unwanted network connections while still allowing WordPress and Coolify to function correctly.

## Install and Configure Coolify 

After preparing the Ubuntu server, install Coolify, which provides a web-based dashboard for deploying and managing applications such as WordPress. Coolify handles service setup, networking, and HTTPS configuration automatically.
The installation is performed using the official Coolify installation script.

### Step 1: Install Coolify
From the server terminal (where you are logged in as the root user), run the following command:

```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```

This command:

- Downloads the official Coolify installation script

- Installs all required dependencies (including Docker)

- Sets up Coolify services on the server

The installation process may take several minutes. During this time, you will see output indicating services being installed and started.

Wait until the command completes and the terminal returns to the prompt: root@hostname:~#. This indicates that the installation has finished.

![](/images/image7.png)

After the installation completes, the terminal displays the server access details, including the **IP address** and **port used** to access the Coolify web interface.

Look for a line similar to the following in the terminal output:

```bash
You can access Coolify using your public IPv4 address at: http://SERVER_IP:8000
```

**Copy this URL** - you'll need it in the next step.

### Step 2 – Access the Coolify Dashboard

Open a web browser on your local machine, such as **Google Chrome**, **Microsoft Edge**, or **Firefox**, and paste the **copied URL** into the address bar:

If you prefer, you can also enter the URL manually by replacing `SERVER_IP` with the IP address of your Ubuntu server.
For example: `http://192.0.2.1:8000`

Press **Enter** to open the URL, and confirm that the Coolify setup screen loads in the browser.

![](/images/image24.png)

### Step 3 – Create the Coolify Account

Create the admin user, which is required to manage servers, applications, databases, and security settings in **Coolify**. This account is used to configure and maintain all resources deployed through Coolify.

This step is **only required once**, during the initial setup.

**Root User Setup Fields**

The table below describes each field in the admin account setup form and explains how to fill it correctly.

| Field          | Description                                                                 |
|----------------|-----------------------------------------------------------------------------|
| **Name**           | Enter a display name for the administrator account (your name or `Admin`). |
| **Email**          | Enter a valid email address (for example, `sara@example.com`). This email is used for account identification and future notifications. |
| **Password**       | Create a strong password that meets the security requirements shown on the screen. |
| **Password again** | Re-enter the same password to confirm it and avoid typing mistakes. |

!!! Important Use a strong password with at least 8 characters, including one uppercase letter, one lowercase letter, one number, and one symbol to protect your Coolify instance from unauthorized access.

![](/images/image34.png)

Click on the **Create Account** button, and Coolify will create the root user and automatically log you in.

![](/images/image36.png)

!!! Important: Save your email address and password securely, as they will be required to log in to Coolify in the future.

### Step 4: Complete Initial Coolify Setup

After the account is created, a welcome screen is displayed. Coolify automatically prepares the server for deployments, including service setup and initial project configuration.

No manual configuration is required. Click **Let’s go!** to continue.

![](/images/image11.png)

**Choose the Server Type**

Coolify supports multiple deployment targets, each intended for a different setup:

- **This Machine** – Deploy applications on the same server where Coolify is installed

- **Remote Server** – Deploy applications on a different server via SSH

- **Hetzner Cloud** – Provision and manage servers directly in Hetzner Cloud

For the demonstration, **This Machine** is selected. A single server is used with Ubuntu 24.04 and Coolify already installed, and both WordPress and MariaDB run on the same machine.


!!! tip If you are already familiar with Coolify and have an existing server configuration, you may skip parts of the setup. For first-time deployments, using this machine is the simplest and recommended option.

![](/images/image21.png)

Create **Your First Project** to set up your first project for testing purposes.

![](/images/image13.png)

You are redirected to the **Coolify dashboard**, where the newly created project is listed and ready for resource deployment.

![](/images/image17.png)

### Step 5: Configure Coolify Instance Domain

Before deploying applications, configure the domain used to access the Coolify instance. This domain is required to enable secure HTTPS access and proper routing within Coolify.

You may use any **domain** or **subdomain** that you control.

!!! warning This guide assumes that the selected domain or subdomain is already pointed to the server’s IP address. DNS configuration at the domain registrar.

**Step1**: In the **Coolify dashboard**, open **Settings** from the left navigation menu.

![](/images/image12.png)

**Step 2**: In the **Domain field**, enter either a subdomain `for example, https://coolify.your-domain.com` or the root domain `for example, https://your-domain.com`, depending on how your DNS is configured. 
Use your actual domain name instead of the example.

**Step 3**: In the **Name field**, enter a label to identify the instance (for example: `Coolify`)

![](/images/image22.png)

Leave all other settings **unchanged**

**Step 4**: Click on the **Save** button, and you will see a confirmation message indicating the action was completed successfully.

![](/images/image33.png)

### Step 6: Access Coolify Using the Configured Domain

1. Once the domain settings are saved, Coolify becomes accessible through the configured domain.
Open a new browser tab and enter the configured domain in the address bar (for example, `https://coolify.your-domain.com`).

2. The Coolify login screen appears. Sign in using the administrator **email** and **password** created during the initial setup.

![](/images/image38.png)

3. Click on the **Login** button to access the Coolify dashboard.

![](/images/image26.png)

### Step 7: Create a New Project

Projects in Coolify are used to group related resources such as applications and databases. In this guide, a dedicated project is created to deploy the WordPress site and its database.

1. From the Coolify dashboard, click on the **Projects** tab in the left navigation menu.

![](/images/image3.png)

2. Click on the **Add** button to create a new project.

![](/images/image37.png)

3. Enter a **name** and **description**  (optional) for the project.
   
**`Example`** Project Name: “ wordpress-production” Description (optional):  “Production WordPress deployment on Ubuntu 24.04 using Coolify”

![](/images/image29.png)

4. Click on the **Continue** button to create the project.

![](/images/image23.png)

## Deployment: WordPress with MariaDB

WordPress requires a database to store website content, user accounts, and configuration settings. In this setup, **MariaDB** is used as the database backend because it is stable, fast, and fully supported by WordPress.

When you select **WordPress with MariaDB** in Coolify, the database is created and connected automatically. No manual database setup is required.

**How MariaDB is added in Coolify**

1. Open your project and click on the **Add Resource** button to install the resource you want.

![](/images/image14.png)

2. Search for **WordPress** and select **WordPress with MariaDB** from the available options.

![](/images/image18.png)

## Configure Domain and Enable HTTPS (Let’s Encrypt SSL)

This step connects your WordPress site to your domain and enables HTTPS using Let’s Encrypt. Coolify automatically provisions and renews SSL certificates once the domain is correctly attached.

!!! Assumption Your domain or subdomain is already pointing to the server’s public IP address. DNS configuration at the registrar.

![](/images/image32.png)

**Attach a domain to WordPress**

1. Open your **WordPress service** within the project, locate **Services**, and click **Settings** (✏️ icon).

![](/images/image27.png)

2. Locate the **Domains** field and enter your domain or subdomain (for example, `blog.your-domain.com`) and click on the **Save** button.

![](/images/image20.png)


### Deploy with SSL enabled

1. Click on the **Deploy** button in the top-right corner.

2. Coolify will:

- Apply the domain

- Request a Let’s Encrypt certificate

- Configure HTTPS automatically

This process may take 1–2 minutes.

![](/images/image10.png)

**Verify HTTPS:** Open your domain in a browser and confirm the site loads successfully, the URL starts with `https://...`, a secure lock (🔒) is visible, and no security warnings appear; if SSL is still provisioning, wait briefly and refresh the page.

Once these checks pass, HTTPS has been successfully enabled for your site.

### Complete WordPress Installation

Once the **deployment is complete** and **HTTPS is active**, open WordPress in a browser and complete the installation using the on-screen setup.

![](/images/image2.png)

1. Select your **preferred language** from the list and click **Continue** to proceed with the WordPress setup.

![](/images/image6.png)
   
2. Provide the basic details required to configure your WordPress site.

- **Site Title**: Enter a name for your website (for example, My Production WordPress Site).
- **Username**: Create a unique administrator username.
- **Password**: Create a strong, secure password for the admin account.
- **Email Address**: Enter the administrator’s email address.

![](/images/image9.png)

1. Click on the **Install WordPress** button to complete the setup. Your WordPress site will now be ready to use.
   
![](/images/image8.png)

A successful confirmation window will appear showing the details of the username and password you chose. Click the **Login** button to access your WordPress account.

![](/images/image35.png)

#### Log in to WordPress

Once the WordPress initial setup is complete, you can log in to the admin dashboard.

1. On the WordPress login screen, enter the **username** or **email** and the **password** you created during setup.

![](/images/image5.png)


1.  Click on the **Log In** button to successfully access your WordPress dashboard.

![](/images/image19.png)

You will be redirected to the **WordPress dashboard**, where you can manage your site’s content, themes, plugins, and settings.

![](/images/image1.png)

## Validation: Verify the WordPress Site Is Live Over HTTPS

This section confirms that the WordPress deployment is complete and accessible securely over HTTPS, as required.

**Verify Site Accessibility**

1. Open a web browser, go to `https://your-domain.com`, and press **Enter** to open your site.

2. Log in to WordPress by entering your username and password to access the dashboard.

3. You will be redirected to the WordPress dashboard, where you can manage your site’s content, themes, plugins, and settings.

![](/images/image1.png)

!!! Info This confirms that your WordPress site is fully loaded and accessible via HTTPS.


### Verify HTTPS (SSL)

Open your WordPress site and confirm the URL starts with `https://...`, or a secure lock (🔒) is visible, and no security warnings appear.

![](/images/image15.png)

!!! note This confirms that the **SSL certificate was successfully issued** and **applied by Coolify using Let’s Encrypt**.

This completes the deployment of a production-ready WordPress site on Ubuntu 24.04 using Coolify, with MariaDB and HTTPS enabled via Let’s Encrypt.

**Next Steps:** With WordPress deployed, you can customize themes and plugins, configure settings, add content, and set up backups and security. For guidance, refer to the [WordPress official](https://wordpress.org/documentation/) documentation. No further server configuration is needed for normal use.
