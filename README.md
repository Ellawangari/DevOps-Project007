# DevOps-Project007

****Project Implementation****

The project tasks were to prepare a DevOps tooling website solution to be accessed by the DevOps team.

****Key Takeaways****
-  Learned  more on Subnet ID  and how to create the servers to be on the same Subnet.
-  With LAMP stack knowledge, I was able to create a web soulution with remote Database and NFS servers.
-  More practice on disk partioning and Volume management.

****STEPS****
**Step 1: Prepare NFS Server**
- Spinned up a EC2 server with RHEL Linux 8 OS.

- Configure LVM on the server.
-  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/4.PNG)

-  Created 3 Logical Volumes(lv-opt lv-apps, and lv-logs)
-  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/7.PNG)

- Formatted the disks as xfs.
-  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/8.PNG)

- Created 3 mount points on the `/mnt` directory for the logical volumes.
-  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/9.PNG)

- Installed NFS server `sudo yum -y update` `sudo yum install nfs-utils -y`
- Started and Enabled the NFS Server.
-  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/10.PNG)

-  Ensured that the Webservers are inside the same Subnet CIDR.
-  Set up  permissions to ensure the web servers can read and execute NFS server files.
  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/11.PNG)
  
- Configure access to NFS for clients within the same subnet by editing the following file `sudo vi /etc/exports`. by adding the following code .`/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)`
- Checked which port is used ny the NFS server and add it as an inbound rule. `rpcinfo -p | grep nfs` also for NFS server to be accesible the following inbound ports had to be opened `TCP 111, UDP 111, UDP 2049`
![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/12.PNG)
![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/13.PNG)

**Step 1: Prepare DATABASE Server**

- Spinned up an Ubuntu Server
- Installed MYSQL Server `sudo apt install mysql -y` ensured the mysql server is running by `sudo systemctl status mysql`
- Created a Database called toolind and created a user called webaccess and granted permission to the user to do anything on the tooling database only from the webservers subnet cidr.
![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/db1.PNG)


**Step 3: Prepare the WEB Servers**
- Launched a new EC2 instance with RHEL 8 OS
- Installed NFS client `sudo yum install nfs-utils nfs4-acl-tools -y`
- Mounted  `/var/www/` and target the NFS server’s export for apps. `sudo mkdir /var/www` `sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www`
![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/web1.PNG)

- Edited the `/etc/fstab` file to ensure the mount changes persisted even after system reboot.
- Installed Remi’s repository, Apache and PHP.
 > sudo yum install httpd -y

 > sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

 >  sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

 > sudo dnf module reset php

 > sudo dnf module enable php:remi-7.4

 > sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

 > sudo systemctl start php-fpm

 > sudo systemctl enable php-fpm

 > setsebool -P httpd_execmem 1

- Forked the tooling repo from my GitHub and clone to the web servers. Copied the html folder to /var/www/html.
 ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/14.PNG)
 
 
 - Updated the  website configuration to work with the database by editing the `/var/www/html/functions.php ` file with `(mysql -h db_ip -u webaccess -p tooling < tooling-db.sql)`
 
- Inserted new user into the database  `(INSERT INTO ‘users’ (’id’, ‘username’, ‘password’, ‘email’, ‘user_type’, ‘status’) VALUES -> (1, ‘myuser’, ‘5f4dcc3b5aa765d61d8327deb882cf99’, ‘user@mail.com’, ‘admin’, ‘1’);)`
  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/18.PNG)
  
  - Repeated the steps 3 for a second webserver.
  
- Logged to website using one of the webservers ip address
  ![alt text](https://github.com/Ellawangari/DevOps-Project007/blob/main/Images/19.PNG)
  
   ****Was an exciting practical experience getting to learn something new with each project and more practice for concepts learned on previous projects .****
  
