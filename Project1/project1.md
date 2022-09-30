# DevOps-Project001
First project from the guided [darey.io](https://www.darey.io) DevOps Engineer Scholarship 

**Project Implementation**
Technology Stack used was **LAMP**( Linux Apache MYSQL PHP)

**I did not know I was supposed to capture screenshots of all the process of the project implementation. However, I was only able to save 2 screenshots. But I followed all the processes till project completion.

**Step 1: Creating an AWS instance & Connecting to it using Putty**

**Step 2: I connected to the instance using putty ssh (I am using a Windows PC)**

**Step 3: Installing Apache in my AWS instance and updating firewall**
   - Connected to the AWS instance on Putty and installed Apache by running the following command to verify that apache is running as a service.
       `sudo systemctl status apache2`

**Step 4: Installing MYSQL **
  - Connected to the AWS instance on Putty and installed MYSQL  and runned the following command to test if i can log in to MYSQL console.
   ` sudo apt install mysql-server`
    ` sudo mysql`
**Step 5: Installing PHP **
  - Connected to the AWS instance on Putty and installed PHP  and runned the following command to check the version installed.
   ` sudo apt install php libapache2-mod-php php-mysql`  ` php -v`
   
**Verifying that PHP has been installed**
    ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project1/php%20intalled.png)
