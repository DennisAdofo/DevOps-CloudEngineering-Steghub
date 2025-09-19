# Implementation of Lempstack Project on AWS
# Overview
# This LEMP stack project is a web service consisting of the following:
* Linux - Operating system that hosts the environment
* Ngnix - Web server that handles HTTP requests
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
 ![AWS EC2 interface](../2.%20Lempstack/images/0.png)
 ---
 * Check status to confirm if the instance has been launced and running
---
![AWS EC2 status](../2.%20Lempstack/images/0.png)
---
 * Copy the public IP Address of your instance
 ---
![Cpying IP address](../1.%20Lampstack/Images/2.png)
---


# Step 2: Connect to your instance
* Open your terminal (Git bash)
---
* ![Connect to terminal](../2.%20Lempstack/images/1.png)
---
Enter download
```bash
 cd downloads
 ```
---
* ![terminal](../2.%20Lempstack/images/0i.png)
---
```bash
 ssh -i lemp.pem ubuntu@<EC2_Public_IP_Address>
 ```
---
* ![connecting](../2.%20Lempstack/images/1.png)
---
# Step 3: Update the system
```bash
sudo apt update
```
# Step 4: Install Nginx Web Server

Install Nginx Web server by typing the commands below:

```bash
sudo apt install nginx
```
```bash
Sudo systemctl enable nginx
```
```bash
sudo systemctl start nginx
```
---
![ngnix install](../2.%20Lempstack/images/2.png)
---

Test : Visit http:// <EC2 Public IP> in a browser

---
![nginx install](../2.%20Lempstack/images/3.png)
---

# Step 5: Install MySQL

Now that your web server is up and running, you need to install the database system to store and manage data for your site. MySQL is a popular database management system used within PHP environments.

Install MySQL by typing the command below:

```bash
sudo apt install mysql-server
```

When propmted, confirm installation by typing Y, and then Enter.
---
![install mysql](../2.%20Lempstack/images/4.png)
---

The above shows that MySQL has been installed also active.

Log into MySQL by typing the command below:

```bash
sudo mysql
```

---
![login mysql](../2.%20Lempstack/images/5.png)
---

--
![login mysql](../2.%20Lempstack/images/5.png)
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
![Change password](../2.%20Lempstack/images/7.png)
---

# Step 6: Install PHP

You have Nginx installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the final user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You will also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

To install these packages, run the following command:

```bash
sudo apt install php libapache2-mod-php php-mysql
```
To check PHP version, type the command below:

```bash
php -v
```


![login mysql](../2.%20Lempstack/images/9.png)
---
# Step 7: Creating a Virtual Host for your website using Apache

* Creat a directory for your website by running the command below:
  
```bash
sudo mkdir /var/www/projectLEMP
```
* Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

```bash
sudo chown -R $USER:$USER /var/www/projectLEMP
```
* Then, create and  open a new configuration file in Nginx sites-available directory using either vi or nano. Here we will use vi.

```bash
sudo vi /etc/apache2/sites-available/projectLEMP.conf
```
* Press i and paste the following into the blank file:
--
```bash
#/etc/nginx/sites-available/projectLEMP
server {
   listen 80;
   server_name projectLEMP www.projectLEMP;
   root /var/www/projectLEMP

   index index.html index.htm index.php;

   location / {
      try_files $uri $uri/ =404;
   }

   location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
   }

   location ~ /\.ht {
      deny all;
   }

}
```

To save and close the file completely:
  * Hit the esc button on the keyboard
  * Type :
  * Type wq. W for write and q for quit
  * Hit Enter to save the file

``` 
* NB: using /var/www/projectLEMP tells Nginx to serve projectLEMP using that as its web root directory.
* listen - Defines what port Nginx should listen on. In our case, port 80, default port of HTTP.
* server_name - defines which ip address or domain names the server should respond to.
```

Activate your configuration by linking to the config file from Nginx's sites-enabled directory:

```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled
```
* NB: The above will tell Nginx to use the configuration when reloaded.


Test your configuration:

```bash
sudo nginx -t
```
![Configuration](../2.%20Lempstack/images/12.png)



Disable default Nginx host that is currently configured to listen on port 80:

```bash
sudo unlink /etc/nginx/sites-enabled/default
```
Finally, reload Nginx so these changes take effect:

```bash
sudo systemctl reload nginx
```
# Step 8: Test PHP Processing on your Web Server

Create a test file for your empty web root

```bash
sudo bash -c 'echo "Hello LEMP from hostname <b>$(TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
-H "X-aws-ec2-metadata-token-ttl-seconds: 21600") && curl -H "X-aws-ec2-metadata-token: $TOKEN" \
-s http://169.254.169.254/latest/meta-data/public-hostname)</b><br>with public IP <b>$(TOKEN=$(curl -X PUT \
"http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600") && \
curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-ipv4)</b>" \
> /var/www/projectLEMP/index.html'

```

Visit your browser and try to open your website URL using IP address:

```bash
http://<EC2_Public_IP_address>:80
```
* You will see this:

![Display](../2.%20Lempstack/images/13.png)

# Step 9: Testing PHP with Nginx
Create a new file called info.php inside the document root:

```bash
nano /var/www/projectLEMP/info.php
```
Add the following text inside the file:

```php
<?php
phpinfo():
?>
```
* NB: phpinfo() return information about your server.
Save and close the file, refresh the page and you will see this:


You can access this page by visiting the ip address in your config file:

```bash
http://`server_domain_or_IP`/info.php
```
* You should see this:
![PHP Display](../2.%20Lempstack/images/14.png)

---
NB: After checking the info, it is best to remove the file as it contains sensitive information about your PHP environment and your Ubuntu server. You use rm to remove that file. You can always get that file if you need it.

---
```bash
sudo rm /var/www/your_domain/info.php
```


# Step 10: Retrieving data from MySQL database with PHP
* Here, We will create a database with To do list and then access it and then let Nginx website query the data to display it.
---
* First, we connect to MySQL console using the root account and enter password:

```bash
sudo mysql -u root -p
```
* We’ll create a database named example_database and a user named example_user

```bash

CREATE DATABASE example_database;
```

 * creates a new user named example_user, using mysql_native_password as default authentication method.

 ```bash
 CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
 ```
* Give user permission over the example_database database:

```bash
GRANT ALL ON example_database.* TO 'example_user'@'%';
```
NB: This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

---

* Exit the MySQL shell with:
```bash
exit
```
* Test if new user has proper permission by relogging in to the console again:
```bash
sudo mysql -u `user` -p
```
* Enter the password when prompted.
* Confirm you have access to the student database:
```bash
SHOW DATABASES;
```
---
* This is the output:
![Display Database](../2.%20Lempstack/images/15.png)
---
# Step 11: Create a Test table

create a test table named todo_list
```bash
CREATE TABLE example_database.todo_list (
	item_id INT AUTO_INCREMENT,
	content VARCHAR(255),
	PRIMARY KEY(item_id)
);
```
Insert a few rows of content in the test table:
```bash
INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
INSERT INTO example_database.todo_list (content) VALUES ("My second important item");
INSERT INTO example_database.todo_list (content) VALUES ("My third important item");
INSERT INTO example_database.todo_list (content) VALUES ("and this one more thing");
```
confirm that data is successfully saved into the table:
```bash
SELECT * FROM example_database.todo_list;
```
![Display Database result](../2.%20Lempstack/images/16.png)
---
Exit:

```bash
exit
```
# Step 12: PHP Scripts that connect to MySQL and Query for the Content
Create a new PHP file in custom web root directory:
```bash
nano /var/www/projectLEMP/todo_list.php
```
The PHP script connects to the MySQL database and queries for the content of the todo_list table, exhibiting the results in a list:

```bash
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>"; 
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
---
Save and close the file when you are done.

---



* Access this page in your web browser by visiting this:
```bash
http://<Public_domain_or_IP>/todo_list.php
```
You should see a page like :

---
![Display Database result](../2.%20Lempstack/images/17.png)

---
End of Details

---