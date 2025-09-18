# Implementation of Lampstack Project on AWS
# Overview
# This LAMP stack project is a web service consisting of the following:
* Linux - Operating system that hosts the environment
* Apache - Web server that handles HTTP requests
* MySQL - Database system that stores and manages data
* PHP - Scripting Language for dynamic content
The Lamp stack was deployed on Amason EC2 instance running on Ubuntu
# Project Prerequisites
* An AWS account
* Basic knowledge of Linux commands
* A key pair for SSH access
* AWS security group rules allowing;
  * HTTP (Port 80) : To serve we traffic
  * SSH (PORT 22) : For secure access
* Local terminal (Git bash)
* Github for central repsoitory, version control and documentation
#  Step by Step Implementation
# Step 1: Launch an EC2 instance
 * Log into the AWS Management console to setup the EC2 Instance
 * Search for EC2 on the search bar
 * Click on Launch Instance
 * Enter the name of the web server
 * Choose Ubuntu server 22.04 or the latest version
 * Select an instance type
 * Configure security group to allow HTTP, HTTPS, SSH
 *  Launch and download the .pem key pair.
 *  Configure the storage to what you prefer
 *  Click on launch instance
 ---
 ![AWS EC2 interface](../1.%20Lampstack/Images/1.png)
 ---
 * Check status to confirm if the instance has been launced and running
---
![AWS EC2 status](../1.%20Lampstack/Images/2.png)
---
 * Copy the public IP Address of your instance
 ---
![Cpying IP address](../1.%20Lampstack/Images/2.png)
---


# Step 2: Connect to your instance
* Open your terminal (Git bash)
---
* ![Connect to terminal](../1.%20Lampstack/Images/3ii.png)
---
Enter download
```bash
 cd downloads
 ```
---
* ![terminal](../1.%20Lampstack/Images/3i.png)
---
```bash
 ssh -i lamp.pem ubuntu@<EC2_Public_IP_Address>
 ```
---
* ![connecting](../1.%20Lampstack/Images/3ii.png)
---
# Step 3: Update the system
```bash
sudo apt update
```
# Step 4: Install Apache Web Server

Install Apache Web server by typing the commands below:

```bash
sudo apt install apache2
```
```bash
Sudo systemctl enable apache2
```
```bash
sudo systemctl start apache2
```
---
![apache install](../1.%20Lampstack/Images/4.png)
---

Test : Visit http:// <EC2 Public IP> in a browser

---
![apache install](../1.%20Lampstack/Images/5.png)
---

# Step 5: Install MySQL

Now that your web server is up and running, you need to install the database system to store and manage data for your site. MySQL is a popular database management system used within PHP environments.

Install MySQL by typing the command below:

```bash
sudo apt install mysql-server
```

When propmted, confirm installation by typing Y, and then Enter.
---
![install mysql](../1.%20Lampstack/Images/6.png)
---

The above shows that MySQL has been installed also active.

Log into MySQL by typing the command below:

```bash
sudo mysql
```

---
![login mysql](../1.%20Lampstack/Images/7.png)
---

--
![login mysql](../1.%20Lampstack/Images/7.png)
---
Change the root user authenication method to the one that uses a password by typing the command below:

 ```bash
  ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
  ```

After making this change, exit the MySQL Prompt by typing:

 ```bash
 exit
 ```

Start the interactive script:

 ```bash
 sudo mysql_secure_installation
 ```

This will ask you if you want to configure and VALIDATE PASSWORD PLUGIN. Type y, if you answer you will be asked to select a alevel of password validation'

--
![Change password](../1.%20Lampstack/Images/8.png)
---

# Step 6: Install PHP

You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the final user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You will also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

To install these packages, run the following command:

```bash
sudo apt install php libapache2-mod-php php-mysql
```
To check PHP version, type the command below:

```bash
php -v
```

--
![login mysql](../1.%20Lampstack/Images/9.png)
---
# Step 7: Creating a Virtual Host for your website using Apache

* Creat a directory for your website by running the command below:
  
```bash
sudo mkdir /var/www/projectlamp
```
* Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

```bash
sudo chown -R $USER:$USER /var/www/projectlamp
```
* Then, create and  open a new configuration file in Apache’s sites-available directory using either vi or nano. Here we will use vi.

```bash
sudo vi /etc/apache2/sites-available/projectlamp.conf
```
* Press i and paste the following into the blank file:
--
![login mysql](../1.%20Lampstack/Images/10.png)
---

To save and close the file completely:
  * Hit the esc button on the keyboard
  * Type :
  * Type wq. W for write and q for quit
  * Hit Enter to save the file
    
With the above VirtualHost configuration, we are telling Apache to serve projectlamp using /var/www/projectlamp as its web root directory.

To enable the new virtual host, run the command belwo:

```bash
sudo a2ensite projectlamp
```
To disable Apache’s default website, run the command below:

```bash
sudo a2dissite 000-default
```
To make sure your configuration file doesn’t contain syntax errors, run the following command:

```bash
sudo apache2ctl configtest
```

Finally, reload Apache so these changes take effect:

```bash
sudo systemctl reload apache2
```
# Step 8: Test PHP Processing on your Web Server

Create a test file for your empty web root

--
![login mysql](../1.%20Lampstack/Images/11.png)
---

Visit your browser and try to open your website URL using IP address:

```bash
http://<EC2_Public_IP_address>:80
```
If you see the text from 'echo' command you wrote to the index.html file, then it means your Apache virtual host is working.

# Step 9: Enable PHP on the Website

To change the behavior of the DirectoryIndex, run the command below:

```bash
sudo vim /etc/apache2/mods-enabled/dir.conf
```
Change: DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm

To: DirectoryIndex index.php index.html index.cgi index.pi index.xhtml index.htm

Save and close the file, then reload Apache with the command below:

```bash
sudo systemctl reload apache2
```
# Create a new file called index.php inside the web root

Run the command below to create index.ph:

```bash
vim /var/www/projectlamp/index.php
```
Add the following text inside the file:

```php
<?php
phpinfo():
?>
```
Save and close the file, refresh the page and you will see this:
--
![login mysql](../1.%20Lampstack/Images/12.png)
---



