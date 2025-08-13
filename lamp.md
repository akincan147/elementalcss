----------------------------------------------------
# STEP 1 — INSTALLING APACHE
----------------------------------------------------

## Install:

sudo apt install apache2

##  Check version:

apache2 -v

##  List available ufw application profiles:

sudo ufw app list

##  Allow Apache Full:

sudo ufw allow in "Apache Full"

##  Enable ufw:

sudo ufw enable

## Check ufw status:

sudo ufw status

## Start apache:

sudo systemctl start apache2

## Restart apache:

sudo systemctl restart apache2

## Check apache status:

systemctl status apache2

## To run on boot (optional):

sudo systemctl enable apache2

## Check if it`s enabled:

systemctl is-enabled apache2





---------------------------
# STEP 2 — INSTALLING MYSQL
---------------------------

## Install:

sudo apt install mysql-server

## Check version:

mysql --version

## Secure the installation:

sudo mysql_secure_installation

## Check status:

systemctl status mysql

## Start mysql (if not running):

sudo systemctl start mysql

## Restart mysql:

sudo systemctl restart mysql





-------------------------
# STEP 3 — INSTALLING PHP
-------------------------

## Install:

sudo apt install php libapache2-mod-php php-mysql

## Check version:

php -v

## Change directory index (optional) 
## Move index.php to the beginning
## Then restart apache:

sudo nano /etc/apache2/mods-enabled/dir.conf

## Test php

## Create info.php file:

sudo touch /var/wwww/html/info.php

## Edit info.php file:

sudo nano /var/wwww/html/info.php

## Add this line and save it:

echo "<?php phpinfo(); ?>"

## Open your browser and go to:

http://localhost/info.php





--------------------------------
# STEP 4 — INSTALLING phpMyAdmin
--------------------------------

## Install
## During installation
## You'll be prompted to select a web server. 
## Choose apache2 (use SPACE to select, then TAB to move to OK).
## Choose YES to configure the database for phpMyAdmin with dbconfig-common.
## Set a password for the phpMyAdmin user when prompted:

sudo apt install phpmyadmin

## Enable extensions after installation:

sudo phpenmod mbstring

sudo systemctl restart apache2

## Enable phpMyAdmin in Apache (if it wasn’t automatically enabled):

sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf

sudo a2enconf phpmyadmin

sudo systemctl reload apache2

## Check version:

apt show phpmyadmin

## Access phpMyAdmin
## Open your browser and go to:
## login with username: "phpmyadmin" (you cant login with "root" as default.)

http://localhost/phpmyadmin

## Fix "Access denied" for MySQL "root" user
## Some systems use "auth_socket" for "root", blocking PHP/web access. 
## To allow normal password login:

## First check what plugin users are using:

sudo mysql

SELECT user, host, plugin FROM mysql.user;

## The edit MySQL config:

sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

## Add under [mysqld]:

mysql_native_password=ON

## Save and restart MySQL:

sudo systemctl restart mysql

## Alter the root user's authentication method and set a password:

sudo mysql

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password'; FLUSH PRIVILEGES;

## Login to MySQL with the root user and the password from the terminal:

mysql -u root -p





--------------
# VIRTUAL HOST
--------------

# Create a Directory for Your Site
# Replace example with your domain.

sudo mkdir -p /var/www/example/public_html

# Set Permissions

sudo chown -R $USER:$USER /var/www/example/public_html

# Add Sample Content

<html>
  <head>
    <title>Welcome to example.com!</title>
  </head>
  <body>
    <h1>Success! The virtual host is working!</h1>
  </body>
</html>

# Create a Virtual Host Configuration File (creates copy of default.conf)

sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/example.conf

# Edit configuration file:

sudo nano /etc/apache2/sites-available/example.conf

# Configuration for example.com

<VirtualHost *:80>
    ServerAdmin webmaster@example.com
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/example/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

# Update Your /etc/hosts File for Local Testing
# If you're not using a real domain:

sudo nano /etc/hosts

# Add:

127.0.0.1   example.com
127.0.0.1   www.example.com

# Define host name
# Open the Apache configuration file:

sudo nano /etc/apache2/apache2.conf

# Add the following line at the end of the file (or modify it if it's already present):

ServerName example

# Enable the Site

sudo a2ensite example.conf

# Disable the Default Site (Optional)

sudo a2dissite 000-default.conf

# Before reload check for errors:

sudo apache2ctl configtest

# Restart Apache

sudo systemctl reload apache2

# Open web browser and navigate to your domain to verify the changes






