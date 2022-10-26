# DevOps-Project7
Third project from the guided [darey.io](https://www.darey.io) DevOps Engineer Scholarship 

**Project Implementation**
Devops Tooling Website Solution

**Step 1: Creating an AWS instance & Connecting to it using Windows Terminal**
- Created an Amazon account and provisioned a RHEL Linux 8 Operating System., SSD Volume Type EC2 Instance with a free tier option.
 ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project7/instances%20launched.png)
- Downloaded my pem file to access my  AWS Ubuntu server using Terminal on my windows pc.

**Step 2: Prepare NFS Server**
- Based on your LVM experience from Project 6, Configure LVM on the Server.
- Instead of formating the disks as ext4 you will have to format them as xfs
    ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project7/formatted%20volumes%20as%20xfs.png)
    ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project7/entire%20setup%20verified.png)
- Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs
- Create mount points on /mnt directory for the logical volumes as follow:
    - Mount lv-apps on /mnt/apps – To be used by webservers
    - Mount lv-logs on /mnt/logs – To be used by webserver logs
    - Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8
- Install NFS server, configure it to start on reboot and make sure it is u and running
     sudo yum -y update
     sudo yum install nfs-utils -y
     sudo systemctl start nfs-server.service
     sudo systemctl enable nfs-server.service
     sudo systemctl status nfs-server.service
- Export the mounts for webservers’ subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security.
To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:
   ![alt text](https://darey.io/wp-content/uploads/2021/07/EC2_subnet.png)
 - Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:
      sudo chown -R nobody: /mnt/apps
      sudo chown -R nobody: /mnt/logs
      sudo chown -R nobody: /mnt/opt
      
      sudo chmod -R 777 /mnt/apps
      sudo chmod -R 777 /mnt/logs
      sudo chmod -R 777 /mnt/opt
      sudo systemctl restart nfs-server.service
      
- Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):
      sudo vi /etc/exports

     /mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
     /mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
     /mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

     Esc + :wq!

     sudo exportfs -arv
- Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)
     rpcinfo -p | grep nfs
   ![alt text](https://darey.io/wp-content/uploads/2021/07/nfs_port.png)
  - Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049
  ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project7/inbound%20rules%20configured.png)
  
**Step 3: Configure the database server**
- By now you should know how to install and configure a MySQL DBMS to work with remote Web Server
- Install MySQL server
- Create a database and name it tooling
- Create a database user and name it webaccess
- Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr

**Step 4: Prepare the Web Servers**
- Launch a new EC2 instance with RHEL 8 Operating System
- Install NFS client
    sudo yum install nfs-utils nfs4-acl-tools -y
  ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project7/nfs%20client%20installed%20on%20all%20three%20web%20servers.png)
- Mount /var/www/ and target the NFS server’s export for apps
    sudo mkdir /var/www
    sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
- Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:
    sudo vi /etc/fstab
- Add following line
    <NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
- Install Remi’s repository, Apache and PHP
     sudo yum install httpd -y 
     sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm 
     sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm 
     sudo dnf module reset php 
     sudo dnf module enable php:remi-7.4 
     sudo dnf install php php-opcache php-gd php-curl php-mysqlnd 
     sudo systemctl start php-fpm 
     sudo systemctl enable php-fpm 
     sudo setsebool -P httpd_execmem 1
   ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project7/apache%2C%20remi's%20reposiroty%20and%20php%20installed%20across%20all%20three%20servers.png)
- Repeat steps 1-5 for another 2 Web Servers.
- Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files – it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other Web Servers.
- Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. Repeat step №4 to make sure the mount point will persist after reboot.
- Fork the tooling source code from Darey.io Github Account to your Github account.(https://github.com/darey-io/tooling.git)
- Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html
    Note 1: Do not forget to open TCP port 80 on the Web Server.
    Note 2: If you encounter 403 Error – check permissions to your /var/www/html folder and also disable SELinux sudo setenforce 0
    To make this change permanent – open following config file sudo vi /etc/sysconfig/selinux and set SELINUX=disabled then restart httpd.
 ![alt text](https://darey.io/wp-content/uploads/2021/07/Tooling-Website-Html.png)
      
 - Update the website’s configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql
 - Create in MySQL a new admin user with username: myuser and password: password:
     INSERT INTO ‘users’ (‘id’, ‘username’, ‘password’, ’email’, ‘user_type’, ‘status’) VALUES
     -> (1, ‘myuser’, ‘5f4dcc3b5aa765d61d8327deb882cf99’, ‘user@mail.com’, ‘admin’, ‘1’);
 - Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php and make sure you can login into the websute with myuser user.
   ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project7/webserver%201%20running.png)
