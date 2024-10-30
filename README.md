# OCS Inventory Installation Guide on Ubuntu

This repository contains a step-by-step guide for installing OCS Inventory on an Ubuntu server, including the necessary components and configurations.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation Steps](#installation-steps)
  - [1. Prepare the Ubuntu Installer](#1-prepare-the-ubuntu-installer)
  - [2. SSH into the Server](#2-ssh-into-the-server)
  - [3. Update and Upgrade the System](#3-update-and-upgrade-the-system)
  - [4. Install the LAMP Stack](#4-install-the-lamp-stack)
  - [5. Install Perl and Required Packages](#5-install-perl-and-required-packages)
  - [6. Enable Apache2 and MariaDB](#6-enable-apache2-and-mariadb)
  - [7. Verify PHP Installation](#7-verify-php-installation)
  - [8. Install Perl Modules](#8-install-perl-modules)
  - [9. Create the OCS Inventory Database](#9-create-the-ocs-inventory-database)
  - [10. Change PHP Configuration](#10-change-php-configuration)
  - [11. Download and Install OCS Inventory](#11-download-and-install-ocs-inventory)
  - [12. Apply Apache2 Configurations for OCS Inventory Server](#12-apply-apache2-configurations-for-ocs-inventory-server)
  - [13. Access the OCS Inventory Web Interface](#13-access-the-ocs-inventory-web-interface)
  - [14. Add Windows PCs to OCS Inventory](#14-add-windows-pcs-to-ocs-inventory)
- [Additional Notes](#additional-notes)
- [License](#license)

## Prerequisites
- A server running Ubuntu (recommended version: 22.04 LTS or later).
- SSH access to the server.

## Installation Steps

### 1. Prepare the Ubuntu Installer
Ensure you have an Ubuntu server environment ready for installation.

### 2. SSH into the Server
```bash
ssh username@ip_address
```

### 3. Update and Upgrade the System
```bash
sudo apt update && sudo apt upgrade -y
```
### 4. Install the LAMP Stack
Install Apache, MariaDB, PHP, and necessary dependencies:
```bash
sudo apt install apache2 libapache2-mod-perl2 libapache2-mod-perl2-dev libapache-dbi-perl libapache-db-perl libapache2-mod-php libarchive-zip-perl mariadb-server composer php-mbstring php-xml php-mysql php-zip php-pclzip php-gd php-soap php-curl php-json -y
```
### 5. Install Perl and Required Packages
Install Git, Curl, Wget, Make, CMake, and necessary Perl modules:
```bash
sudo apt install git curl wget make cmake gcc -y
sudo apt install perl libxml-simple-perl libcompress-zlib-perl libdbi-perl libdbd-mysql-perl libnet-ip-perl libsoap-lite-perl libio-compress-perl libapache-dbi-perl libapache2-mod-perl2 libapache2-mod-perl2-dev -y
```
### 6. Enable Apache2 and MariaDB
Check and enable services:
```bash
Copy code
sudo systemctl is-enabled apache2
sudo systemctl status apache2
sudo systemctl is-enabled mariadb
sudo systemctl status mariadb
```
### 7. Verify PHP Installation
Check the PHP version:
```bash
Copy code
php -v
```
#### Verify Composer installation:
```bash
sudo -u www-data composer -v
```
### 8. Install Perl Modules
Install additional Perl modules:
```bash
sudo perl -MCPAN -e 'install Apache2::SOAP'
sudo perl -MCPAN -e 'install XML::Entities'
sudo perl -MCPAN -e 'install Net::IP'
sudo perl -MCPAN -e 'install Apache::DBI'
sudo perl -MCPAN -e 'install Mojolicious'
sudo perl -MCPAN -e 'install Switch'
sudo perl -MCPAN -e 'install Plack::Handler'
```
### 9. Create the OCS Inventory Database
Access MySQL and create the database and user:
```bash
sudo mysql -uroot -p
```
#### Inside the MySQL shell:
sql
```bash
CREATE DATABASE ocsdb;
CREATE USER 'ocsuser'@'localhost' IDENTIFIED BY 'ocsPWD';
GRANT ALL PRIVILEGES ON ocsdb.* TO 'ocsuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
### 10. Change PHP Configuration
Edit the PHP configuration file:
```bash
sudo nano /etc/php/8.1/apache2/php.ini
Modify the following settings:
```
ini
```bash
memory_limit = 512M
post_max_size = 100M
upload_max_filesize = 100M
max_execution_time = 360
date.timezone = Europe/Stockholm
```
Restart Apache:
```bash
sudo systemctl restart apache2
```
11. Download and Install OCS Inventory
Download OCS Inventory and extract it:

```bash
sudo wget https://github.com/OCSInventory-NG/OCSInventory-ocsreports/releases/download/2.10.0/OCSNG_UNIX_SERVER-2.10.0.tar.gz
sudo tar -xvf OCSNG_UNIX_SERVER-2.10.0.tar.gz
cd OCSNG_UNIX_SERVER-2.10.0
```
Edit the setup script to add database credentials:
```bash
sudo nano setup.sh
```
Set the following variables:
```bash
DB_SERVER_USER="ocsuser"
DB_SERVER_PWD="ocsPWD"
```
Run the setup script:
```bash
sudo ./setup.sh
```
### 12. Apply Apache2 Configurations for OCS Inventory Server
Link the configuration files:
```bash
sudo ln -s /etc/apache2/conf-available/ocsinventory-reports.conf /etc/apache2/conf-enabled/ocsinventory-reports.conf
sudo ln -s /etc/apache2/conf-available/z-ocsinventory-server.conf /etc/apache2/conf-enabled/z-ocsinventory-server.conf
sudo ln -s /etc/apache2/conf-available/zz-ocsinventory-restapi.conf /etc/apache2/conf-enabled/zz-ocsinventory-restapi.conf
```
Set permissions and restart Apache:

```bash
sudo chown -R www-data:www-data /var/lib/ocsinventory-reports/
sudo systemctl restart apache2
```
### 13. Access the OCS Inventory Web Interface
Open your web browser and navigate to:


http://192.168.1.50/ocsreports/index.php
For security, rename the install file:

```bash
cd /usr/share/ocsinventory-reports/ocsreports/
sudo mv install.php install.php.bak
```
### 14. Add Windows PCs to OCS Inventory
Follow the appropriate steps for adding Windows PCs to the OCS Inventory system.

### Additional Notes
Make sure to adjust the timezone and memory settings based on your server's requirements.
Keep security best practices in mind, such as using strong passwords and firewall configurations.

### License
This project is licensed under the MIT License - see the LICENSE file for details.
