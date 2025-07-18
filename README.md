# Setting Up a Virtual Linux Web Application Test Server on VMware vSphere 7.0

## PURPOSE OF THE DOCUMENT

This document aims to standardize and regulate the entire process of setting up a virtual Linux server for web application testing in a VMware vSphere 7.0 environment. By following this Standard Operating Procedure (SOP), we can ensure consistent virtual machine (VM) configuration, proper resource allocation, compliance with security settings, and correct installation and configuration of all core software required for web application development and testing. This approach guarantees the stability and reliability of the test environment and helps to improve deployment efficiency.

## AUDIENCE

This SOP is intended for system administrators, DevOps engineers, Quality Assurance (QA) teams, and other technical personnel who are responsible for configuring and maintaining virtual machines specifically used for web application testing within the organization.

## APPROVAL TABLE

| Version | Date       | Name     | Designation |
| :------ | :--------- | :------- | :---------- |
| 1.0     | 2025-07-18 | Wu Lu    | Author      |
| 1.1     | yy-mm-dd   | Tiantian Li | Reviewer    |
| 1.1     | yy-mm-dd   | Deepshikha Russell | Approver    |

## SCOPE/OBJECTIVES

This SOP covers the complete process of creating a virtual Linux server as a web application testing environment in VMware vSphere 7.0. Its specific objectives include:

1.  Pre-creation planning and requirements assessment, including defining the technology stack and resource needs.
2.  Configuring the virtual machine settings within VMware vSphere 7.0.
3.  Installing and performing initial setup of the chosen Linux guest operating system.
4.  Installing and configuring essential software components required for web application testing.
5.  Post-creation verification, functional testing, and basic security hardening of the virtual machine.
6.  Detailed documentation of the virtual machine's configuration for future reference and knowledge transfer.

## ACCOUNTABILITY MATRIX

| Task/Steps                         | Responsible Person/Team             | Emergency Contact          | Non-Emergency Contact      | Review/Approval       |
| :--------------------------------- | :---------------------------------- | :------------------------- | :------------------------- | :-------------------- |
| Pre-creation Planning and Assessment | System Administrator / Project Manager | Phone: 4316684305 <br />Email:wulu@student.mitt.ca | Phone: 2042915505 <br />Email:feiwu0755@gmail.com | IT Manager            |
| Configuration of Virtual Machine   | Virtualization Team / System Administrator | Phone: Email/Distribution List | Phone: Email/Distribution List | IT Operations Manager |
| Installation of Guest OS           | System Administrator                | Phone: Email/Distribution List | Phone: Email/Distribution List | IT Manager            |
| Installation & Configuration of Required Software | DevOps Engineer / System Administrator | Phone: Email/Distribution List | Phone: Email/Distribution List | IT Operations Manager |
| Post-creation Verification and Testing | Quality Assurance Team / Developer  | Phone: Email/Distribution List | Phone: Email/Distribution List | QA Manager            |
| Documentation of Virtual Machine   | System Administrator / DevOps Engineer | Phone: Email/Distribution List | Phone: Email/Distribution List | Author                |

---

## EXECUTION STEPS

### Step 1: Pre-creation Planning and Assessment

#### Step 1.1: Identify Purpose and Requirements

*   **Determine the specific purpose of the virtual machine:** To serve as an environment for web application development, testing, or User Acceptance Testing (UAT).
*   **Define the Technology Stack:** For this SOP, a **LAMP (Linux, Apache, MySQL, PHP)** stack will be used, with the following specific components and versions:
    *   **Operating System:** Ubuntu Server 22.04 LTS
    *   **Web Server:** Apache2
    *   **Database:** MySQL Server
    *   **Programming Language:** PHP 8.1+
    *   **Version Control:** Git
*   **Define Resource Requirements:**
    *   **CPU Cores:** 2 vCPUs
    *   **RAM:** 4 GB
    *   **Disk Space:** 60 GB
*   **Network Configuration:**
    *   **Virtual Machine Name:** `Web-Test-Server-01`
    *   **Static IP Address:** `192.168.1.100`
    *   **Subnet Mask:** `255.255.255.0`
    *   **Default Gateway:** `192.168.1.1`
    *   **DNS Servers:** `8.8.8.8`

#### Step 1.2: Plan Resource Allocation

*   Consider the host server's capacity and availability to ensure sufficient CPU, memory, and storage resources are available for the new VM.
*   Plan for CPU and memory reservations or limits as needed to optimize performance.
*   Estimate storage requirements and select an appropriate datastore, preferably one with good performance (e.g., SSD-backed storage).

#### Step 1.3: Communicate with Stakeholders

*   Consult with relevant stakeholders, such as development and QA teams, to confirm the virtual machine's specifications, including hostname, IP address, and software versions.

### Step 2: Configuration of Virtual Machine

#### Step 2.1: Access VMware vSphere Client

*   Launch the vSphere Client and connect to the vCenter Server.
*   Navigate to the target host or cluster where the VM will be created.
*   Right-click and select "New Virtual Machine."
*   Choose "Create a new virtual machine."

#### Step 2.2: Configure Virtual Machine Settings

*   **Specify Name and Location:**
    *   **Virtual Machine Name:** `Web-Test-Server-01`
    *   **Compatibility:** Select a compatible version suitable for your vSphere environment.
    *   **Guest OS Family:** Linux
    *   **Guest OS Version:** Ubuntu Linux (64-bit)
*   **Set CPU and Memory Allocation:**
    *   **CPU:** 2 vCPUs
    *   **Memory:** 4096 MB (4 GB)
*   **Allocate Disk Space and Choose Storage Policies:**
    *   **New Hard disk:** 60 GB
    *   **Disk Provisioning Type:** Select "Thin Provision" to optimize storage space utilization.
*   **Configure Network Settings:**
    *   **Network Adapter:** VMXNET3 (recommended for better performance).
    *   **Connected:** Ensure this box is checked.
    *   **Connect at power on:** Ensure this box is checked.
    *   **Network:** Select `VM Network` (or the specific port group used in your environment).

### Step 3: Installation of Guest OS (Ubuntu Server 22.04 LTS)

#### Step 3.1: Attach Installation ISO

*   In the VM settings, go to "CD/DVD Drive" and select "Datastore ISO File." Browse for and attach the `ubuntu-22.04-live-server-amd64.iso` file.
*   Configure the virtual machine to boot from the attached CD/DVD drive.

#### Step 3.2: Install Guest OS

*   Power on the virtual machine.
*   **Follow the Ubuntu Server installation wizard:**
    *   Select your language and keyboard layout.
    *   **Network Configuration:** Choose "Manual" network configuration and enter the planned static IP address (`192.168.1.100`), subnet mask (`255.255.255.0`), gateway (`192.168.1.1`), and DNS server (`8.8.8.8`).
    *   **Storage Configuration:** Select "Use an entire disk" and proceed with the default partitioning.
    *   **User Account Creation:** Create a user account named `mittadmin` and set a strong password.
    *   **OpenSSH Server:** Ensure the "Install OpenSSH server" option is selected for remote access.
    *   Complete the installation process and reboot the virtual machine when prompted.

#### Step 3.3: Initial OS Configuration

*   Log in to the newly installed Ubuntu server via SSH or the vSphere console using the `mittadmin` user.
*   **Update package lists and upgrade installed packages:**
    ```bash
    sudo apt update
    sudo apt upgrade -y
    ```
*   **Configure the system timezone:**
    ```bash
    sudo timedatectl set-timezone Asia/Shanghai # Adjust as per your geographic location
    ```

### Step 4: Install Required Software Packages for Web Application Testing

#### Step 4.1: Install Apache2 Web Server

*   **Install Apache2:**
    ```bash
    sudo apt install apache2 -y
    ```
*   **Enable and start the Apache2 service:**
    ```bash
    sudo systemctl enable apache2
    sudo systemctl start apache2
    ```
*   **Verification:** Open a web browser on your host machine and navigate to the VM's IP address (`http://192.168.1.100`). You should see the Apache default welcome page.

#### Step 4.2: Install MySQL Server

*   **Install MySQL Server:**
    ```bash
    sudo apt install mysql-server -y
    ```
*   **Run the MySQL secure installation script:**
    ```bash
    sudo mysql_secure_installation
    ```
    *   Follow the on-screen prompts to set a strong root password for MySQL, remove anonymous users, disable remote root login, and remove the test database.
*   **Enable and start the MySQL service:**
    ```bash
    sudo systemctl enable mysql
    sudo systemctl start mysql
    ```
*   **Verification:** Check the MySQL service status: `sudo systemctl status mysql` or attempt to log in using `mysql -u root -p`.

#### Step 4.3: Install PHP and Apache PHP Module

*   **Install PHP and common modules (for PHP 8.1+, modules can be adjusted based on needs):**
    ```bash
    sudo apt install php libapache2-mod-php php-mysql php-cli php-json php-gd php-curl php-mbstring php-xml -y
    ```
*   **Enable the PHP module for Apache (adjust version if different):**
    ```bash
    sudo a2enmod php8.1
    ```
*   **Restart Apache2 service to apply changes:**
    ```bash
    sudo systemctl restart apache2
    ```
*   **Verification (create and remove a test file):**
    *   Create an `info.php` file in the `/var/www/html/` directory:
        ```bash
        echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
        ```
    *   In a browser, visit `http://192.168.1.100/info.php`. You should see the PHP information page.
    *   **IMPORTANT:** For security reasons, delete `info.php` after verification:
        ```bash
        sudo rm /var/www/html/info.php
        ```

#### Step 4.4: Install Version Control and Common Development Tools

*   **Install Git and other useful tools:**
    ```bash
    sudo apt install git vim curl wget unzip -y
    ```

#### Step 4.5: Configure Firewall (UFW)

*   **Allow SSH (port 22) access:**
    ```bash
    sudo ufw allow OpenSSH
    ```
*   **Allow Web services (HTTP and HTTPS) access:**
    ```bash
    sudo ufw allow 'Apache Full'
    ```
*   **Enable the UFW firewall:**
    ```bash
    sudo ufw enable
    ```
*   **Verify firewall rules:**
    ```bash
    sudo ufw status
    ```

### Step 5: Post-creation Verification and Testing

#### Step 5.1: Verify Installation

*   Ensure that the guest operating system is successfully installed and accessible via SSH.
*   Confirm that all core services (Apache2, MySQL, SSH) are running without errors.
    *   `sudo systemctl status apache2`
    *   `sudo systemctl status mysql`
    *   `sudo systemctl status ssh`

#### Step 5.2: Functional Testing

*   **Web Access Test:** Access the virtual machine's IP address (`http://192.168.1.100`) via a web browser to confirm that the Apache default page or a deployed test web application displays correctly.
*   **Database Connection Test:** Attempt to connect to the MySQL database from within the server (e.g., `mysql -u mittadmin -p`) or via a simple PHP script to verify the web application's database connectivity.
*   **Application Deployment Test:** Deploy a basic web application (e.g., a PHP script that interacts with MySQL) to the `/var/www/html` directory and verify its functionality.
*   **Network Connectivity Test:** Test the virtual machine's connectivity to external networks, for example, by running `ping google.com`.

#### Step 5.3: Security Compliance

*   Verify that the firewall (UFW) rules are correctly applied, allowing only necessary inbound connections (SSH and Web services).
*   Consider implementing advanced security measures, such as disabling SSH password authentication in favor of SSH key-based authentication.
*   Ensure all installed software packages are up-to-date and plan for regular security updates.

### Step 6: Documentation of Virtual Machine

#### Step 6.1: Document Virtual Machine Details

*   Record all relevant configuration and settings, including:
    *   VMware configuration (vCPU count, RAM size, disk size, provisioning type, network adapter type, port group name).
    *   Linux operating system version (Ubuntu Server 22.04 LTS).
    *   Assigned IP address (`192.168.1.100`), subnet mask, gateway, and DNS servers.
    *   Hostname (`Web-Test-Server-01`).
    *   `mittadmin` user credentials and password policy (or SSH key location).
    *   Versions of installed key software (Apache2, MySQL Server, PHP 8.1+, Git) and any custom configuration paths.
*   Update the organization's system documentation or Configuration Management Database (CMDB) with the new virtual machine information.

#### Step 6.2: Knowledge Transfer

*   Provide relevant teams (e.g., developers, QA personnel) with necessary information and guidance for accessing and managing the virtual machine. This includes details on SSH access, web application file locations, and database access information, enabling them to effectively utilize and maintain the test environment.

---

## REVISION HISTORY

| Version | Date       | Changes Made By           | Summary of Changes                                                  |
| :------ | :--------- | :------------------------ | :------------------------------------------------------------------ |
| 1.0     | 2025-07-18 | Wu Lu (wu.lu@mitt.ca) | Initial Release of Document for Linux Web Application Test Server Setup |
| 1.1     | yy-mm-dd   | Name/Email                | Which Section/What was added, removed                               |
| 1.2     | yy-mm-dd   | Name/Email                | Which Section/What was added, removed                               |
