# WordPress Installation on Rocky Linux

WordPress is a widely-used and user-friendly content management system that plays a significant role on the internet. Known for its simplicity and flexibility, WordPress allows users to easily create and manage websites, whether it's a personal blog or a complex e-commerce platform.

Fundamentally, WordPress separates website content from design, providing an intuitive interface for content creation and editing. Visual appearance is controlled by themes, and functionality can be extended through plugins, enabling users to customize their websites according to their specific needs.

Supported by a lively community and a vast selection of themes and plugins, WordPress caters to a wide range of online projects. It serves as an accessible platform for individuals, businesses, and developers, equipping them with the necessary tools to bring their ideas to life on the web.

In this guide, we'll walk through the process of deploying WordPress on Rocky Linux 8, offering users a step-by-step guide into leveraging the power of this influential content management system within a distinct Linux environment.

## Installation Steps

### 1. Update and Upgrade the System

```bash
# Update the package list to fetch information about available software packages. This step is crucial for obtaining the most recent versions of packages and ensuring that the system is aware of the latest updates.
sudo dnf update

# Upgrade installed packages to their latest versions for enhanced stability and security. The second command, sudo dnf upgrade -y, initiates the upgrade process, where installed packages are updated to their latest versions. The -y flag is used to automatically confirm the upgrade, streamlining the process.
sudo dnf upgrade -y

# Reboot the system to apply any kernel updates, ensuring optimal performance and security features. This step is essential to apply any kernel updates successfully.
sudo reboot
```
### 2. Search For and Install Apache

```bash
# Install the Apache HTTP server, a foundational component for web hosting. The Apache HTTP server is installed, laying the groundwork for hosting websites. Apache is a widely-used web server, known for its reliability and performance.
sudo dnf install httpd

# List unit files related to Apache to check if it is enabled. This provides a list of unit files related to the Apache service. This step allows users to check if Apache is already enabled.
systemctl list-unit-files httpd.service

# If Apache is disabled, use the following command to enable it as a system service.
sudo systemctl enable httpd


```

### 3. Install W3M
```bash
# Install w3m and w3m-img, enhancing command-line browsing with text and image support. W3M provides a efficient solution for browsing the web through the command line.
sudo dnf install w3m w3m-img
```

### 4. Install Nano
```bash
# Install Nano, a text editor for command-line editing.
sudo dnf install nano
```

### 4. Install and Configure PHP
```bash
# Install PHP and related packages for web scripting and server-side processing
sudo dnf install php php-cli php-mbstring php-common

# Create info.php file in nano to display PHP configuration details
cd /var/www/html
sudo nano info.php
<?php
phpinfo();
?>
# Save the file

# Navigate to the Apache configuration directory
cd /etc/httpd/conf

# Edit the httpd.conf file to prioritize index.php
sudo nano httpd.conf
# Place 'index.php' after DirectoryIndex and before index.html
# Save the file

# Check Apache configuration for syntax errors
sudo apachectl configtest

# If Syntax Ok, reload and restart Apache2
sudo systemctl reload httpd
sudo systemctl restart httpd

# Create a basic index.php for browser detection
cd /var/www/html/
sudo nano index.php

<html>
<head>
<title>Broswer Detector</title>
</head>
<body>
<p>You are using the following browser to view this site:</p>

<?php
echo $_SERVER['HTTP_USER_AGENT'] . "\n\n";

$browser = get_browser(null, true);
print_r($browser);
?>
</body>
</html>
# Save

# Install updated PHP 7.4 throught the Remi PHP Repository, since the packaged PHP is out of date and will not work how we want it to
sudo dnf install \
    https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm \
    https://dl.fedoraproject.org/pub/epel/epel-next-release-latest-8.noarch.rpm

# Import Remi EL 8 repository that contains PHP
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y

# Enable PHP 7.4 from Remi repository
dnf module list php
sudo dnf module enable php:remi-7.4 -y

# Install PHP 7.4
sudo dnf install php php-cli -y

# Confirm PHP installation
php -v

# Install additional PHP extensions for enhanced functionality
sudo dnf install php-cli php-fpm php-curl php-mysqlnd php-gd php-opcache php-zip php-intl php-common php-bcmath php-imagick php-xmlrpc php-json php-readline php-memcached php-redis php-mbstring php-apcu php-xml php-dom php-redis php-memcached php-memcache
```

### 5. Install and Setting Up MariaDB
```bash
# Install MariaDB server and client for database management. MariaDB, an open-source database system, is installed along with its server and client components.
sudo dnf install mariadb-server mariadb

# Check the status of MariaDB service. This checks the status of the MariaDB service, providing information about whether it is active or inactive.
sudo systemctl status mariadb

# If MariaDB is inactive, start the service
sudo systemctl start mariadb

# Run the post-installation script to enhance security and set a root password
sudo mysql_secure_installation

# To access MariaDB as the root user, switch to root and enter MariaDB
sudo su
cd
mysql -u root -p
```

### 6. Install PHP and MySQL Support
```bash
# Install the PHP MySQL extension for seamless interaction between PHP and MySQL
sudo dnf install php-mysqlnd

# Restart Apache to apply the PHP MySQL extension
sudo systemctl restart httpd

# Restart MariaDB to ensure compatibility with the PHP MySQL extension
sudo systemctl restart mariadb
```

### 7. Create PHP Scripts
```bash
# Navigate to the web server's HTML directory
cd /var/www/html/

# Create a new PHP script file named 'login.php'
sudo touch login.php

# Set permissions for the 'login.php' file
sudo chmod 640 login.php

# Change ownership of the 'login.php' file to the Apache web server user
sudo chown :apache login.php

# List the details of the 'login.php' file, confirming permissions and ownership
ls -l login.php

# Open the 'login.php' file for editing using the Nano text editor
sudo nano login.php
```

### 8. Installing Wordpress
```bash
# Install additional PHP extensions required by WordPress
sudo dnf install php-curl php-xml php-imagick php-mbstring php-zip php-intl

# Restart Apache to apply the new PHP extensions
sudo systemctl restart httpd

# Restart MariaDB to ensure compatibility with WordPress
sudo systemctl restart mariadb

# Download the latest version of WordPress from the official website
sudo wget https://wordpress.org/latest.tar.gz

# Extract the WordPress archive to the current directory
sudo tar -xzvf latest.tar.gz
```

### 9. Create the Database and a User
```bash
# Switch to the root user for elevated privileges
sudo su

# Log in to MariaDB as the root user
mysql -u root -p

# Execute the following commands within the MariaDB shell:
create user 'wordpress'@'localhost' identified by 'Your Password';
create database wordpress;
grant all privileges on wordpress.* to 'wordpress'@'localhost';
show databases;
\q

# Set up the WordPress configuration file 'wp-config.php'
cd /var/www/html/wordpress
sudo cp wp-config-sample.php wp-config.php

# Open 'wp-config.php' for editing
sudo nano wp-config.php

# Change the database name and username to 'wordpress', and set the password to your chosen password
# Add the following line at the very bottom to disable FTP
define('FS_METHOD','direct');

# Change ownership of WordPress files to the Apache web server user
sudo chown -R apache:apache .
```

### 10. Run the Install Script
```bash
# After completing the previous steps, open a web browser and navigate to the following address:
# Replace '11.111.111.11' with the actual IP address of your VM
http://11.111.111.11/wordpress/wp-admin/install.php
```

Congratulations! If you follow these instructions closely, you should now have wordpress operating correctly on your Rocky Linux 8 VM. 
