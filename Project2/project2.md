# DevOps-Project2
Second project from the guided [darey.io](https://www.darey.io) DevOps Engineer Scholarship

**Project Implementation**
Technology Stack used was **LEMP**( Linux Nginx MYSQL PHP)

**Step 1: Creating an AWS instance & Connecting to it using Putty**
- Created an Amazon account and provisioned an Ubuntu Server 22.04 LTS (HVM), SSD Volume Type EC2 Instance with a free tier option.
- ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/aws%20instance%20launch.png)
- Set up a putty connection to the server
- - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/putty%20ssh.png)
- Successful connection into the server
- ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/ssh%20into%20server.png)

**Step 2: Installing the Nginx Web Server**
-After connecting to the Ubuntu server, I ran the following code to update my server and then install nginx on the server
    'sudo apt update'
    'sudo apt install nginx'
 Then I ran the code below to verify that nginx has been successfully installed
     'sudo systemctl status nginx'
     ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/nginx%20running%20on%20web%20browser.png)
     
**Step 3: Installing MySQL**
- I ran the code below in my putty connection to the server to install mysql to the server.
- 'sudo apt install mysql-server'
- After the installation was complete, I ran the following code to start mysql
- 'sudo mysql'
- ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/mysql%20installation%20and%20login.png)

**Step 4: Installiing PHP**
-Because nginx requires an external program to handle php processing, I had to install the php dependencies package while installing php, and I did that by running the code below:
'sudo apt install php-fpm php-mysql'
-![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/php%20installation%20with%20dependencies.png)
-![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/php%20installation%20complete.png)

**Step 5: Configuring Nginx to use php processor**
-To get nginx to use php, I went ahead to configure by creating a root web directory, assigning ownership to the directory and then creating a file inside the directory where I put a php script.
-I ran the following code to create a root directory
    -'sudo mkdir /var/www/projectLEMP'
-I ran the following code to assign ownership to the root web directory
    -'sudo chown -R $USER:$USER /var/www/projectLEMP'
-I ran the following code to create a file that I put my php script inside
    -'sudo nano /etc/nginx/sites-available/projectLEMP'
-I put the following code inside the newly created file
    -#/etc/nginx/sites-available/projectLEMP

    server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

-Then after that, I activated my configuration by linking the config file from the root web directory. I achieved this by running the following code
    -'sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/'
-After that, I checked for syntax error by running the following code
    -'sudo nginx -t'
-![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/checking%20for%20syntax%20error.png)

-Then I disabled the default Nginx host that is currently configured to listen on port 80 by running the code below:
    -'sudo unlink /etc/nginx/sites-enabled/default'

-Next thing I did was to reload nginx by running the code below
    -'sudo systemctl reload nginx'

-After that, I created an index.html file by running the following command:
    -'sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html'

-![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/lemp%20works.png)

**Step 6: Retrieving data from mysql database with php**
-First, I connected to mysql by running the code below
  -'sudo mysql'
-Next, I created a new database by running the code below
  -'mysql> CREATE DATABASE `example_database`;'
-Then I created a new user with password by running the code below:
  -'mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';'
-Then I granted the user all permissions by running the code below
  -'mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';'
 Then I exited mysql and logged back in as the new user by executing the following command:
  -'mysql -u example_user -p'
 -After which, I created a new table in the database called todo_list , by executing the following command
 -'CREATE TABLE example_database.todo_list (
    mysql>     item_id INT AUTO_INCREMENT,
           content VARCHAR(255),
           PRIMARY KEY(item_id)
            );'
 - And then I added a few more rows to my newly created table by running the following code several times:
    - 'mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");'
 - To confirm my table works, I ran the following code:
     - 'mysql>  SELECT * FROM example_database.todo_list;'
-After that I exited mysql
-Next step was I created a php file with same name as the table I created earlier, by running the following code:
    -'nano /var/www/projectLEMP/todo_list.php'
-Then I copied the content below into the newly created php file
  '<?php
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
}'
And then I saved and closed the text editor.
-To confirm that nginx was able to interract with both php and mysql, I opened my server on a web browser and it showed me the screen below:
    -![alt text](https://github.com/guruchidi/darey.io/blob/main/Project2/todo%20list.png)
