# WordPress Installation on Rocky Linux

This repository documents the steps to install WordPress on a Rocky Linux server.

## Installation Steps

### 1. Update and Upgrade the System

```bash
# Update the package list and install any available updates
sudo dnf update

# Upgrade installed packages to the latest versions
sudo dnf upgrade -y

# Reboot the system to apply any kernel updates
sudo reboot
```
### 2. Search For and Install Apache

```bash
# Search for Apache-related packages
sudo dnf search httpd | head

# View details of the Apache HTTP Server package
sudo dnf info httpd

# Install the Apache HTTP server
sudo dnf install httpd

# List unit files related to Apache
systemctl list-unit-files httpd.service

# Check the status of the Apache service
sudo systemctl status httpd
```

### 3. Install W3M
```bash
# Install w3m and w3m-img
sudo dnf install w3m w3m-img
```

### 4. Install Nano
```bash
sudo dnf install nano
```

### 4. Install and Configure PHP
```bash
# Install PHP and related packages
sudo dnf install php php-cli php-mbstring php-common

# Navigate to the Apache configuration directory
cd /etc/httpd/conf

# Edit the httpd.conf file
sudo nano httpd.conf

# Check Apache2 configuration
sudo apachectl configtest

# If Syntax Ok, reload and restart Apache2
sudo systemctl reload httpd
sudo systemctl restart httpd
```

### 5. Install and Setting Up MariaDB
```bash
# Install MariaDB server and client
sudo dnf install mariadb-server mariadb

# Access MariaDB as the root user
mysql -u root -p

#Create and Setup New User Account
create user 'webapp'@'localhost' identified by 'XXXXXXXXX';
```

### 6. Install PHP and MySQL Support
```bash
# Install the PHP MySQL extension
sudo dnf install php-mysqlnd

# Restart Apache
sudo systemctl restart httpd

# Restart MariaDB
sudo systemctl restart mariadb
```

### 7. Create PHP Scripts
```bash
cd /var/www/html/
sudo touch login.php
sudo chmod 640 login.php
sudo chown :apache login.php
ls -l login.php
sudo nano login.php
```

### 8. Installing Wordpress
```bash
# Install PHP extensions
sudo dnf install php-curl php-xml php-imagick php-mbstring php-zip php-intl

# Restart Apache
sudo systemctl restart httpd

# Restart MariaDB
sudo systemctl restart mariadb

# Download the latest version of WordPress
sudo wget https://wordpress.org/latest.tar.gz

# Extract the WordPress archive
sudo tar -xzvf latest.tar.gz
```
### 9. Create the Database and a User
```bash
# Switch to the root user
sudo su

# Log in to MariaDB as the root user
mysql -u root -p

# Enter the following:
create user 'wordpress'@'localhost' identified by 'XXXXXXXXX';
create database wordpress;
grant all privileges on wordpress.* to 'wordpress'@'localhost';
show databases;
\q

#Change File Ownership
sudo chown -R apache:apache .
```



