# Documentation for Project 6 - WordPress Solution
- Step 1 -- Setup Servers on AWS 
  
   - `sudo apt-get update && sudo apt-get upgrade` 

   -  ![Connecting to the EC2-instance](./images/Capture-ClientConnectServer.JPG)
  
### Create volumes, mount volumes, partion volumes
    ```
        sudo gdisk /dev/xvdf
        sudo yum install lvm2
        sudo lvmdiskscan
        sudo pvcreate /dev/xvdf1
        sudo pvcreate /dev/xvdg1
        sudo pvcreate /dev/xvdh1
        sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
        sudo lvcreate -n apps-lv -L 14G webdata-vg
        sudo lvcreate -n logs-lv -L 14G webdata-vg
        sudo vgdisplay -v #view complete setup - VG, PV, and LV
        sudo lsblk 
        sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
        sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
    ```

  - ![Concatenting Volumes](./images/Capture-CocatVolume.JPG)
  - ![Creating Logicial Volume Mounts](./images/Capture-CreateLVM.JPG)
  - ![Creating Logicial Volume Mounts](./images/Capture-LVMConfigured-DB.JPG)
  - ![Mount Partitions](./images/Capture-MountPartitions.JPG)
  - ![Mount Partitions](./images/Capture-MountDirectory.JPG)

### Create Logical mount parts

    ```
    sudo mkdir -p /var/www/html
    sudo mkdir -p /home/recovery/logs
    sudo mount /dev/webdata-vg/logs-lv /var/log
    sudo rsync -av /home/recovery/logs/. /var/log
    sudo nano /etc/fstab 
    ```
  - ![Create Physical Volumes](./images/Capture-PVcreate.JPG)
  - ![Creating Volume Groups](./images/Capture-VGcreate.JPG)
  - ![Sync Files](./images/Capture-SyncFiles-1.JPG)
  - ![Sync Files](./images/Capture-SyncFiles-2.JPG)
  - ![Edit fstab](./images/Capture-EditFSTAB.JPG)
  
    ```
    sudo mount -a
    sudo systemctl daemon-reload
    ```

### Install WordPress on your Web Server EC2
    ```
    sudo systemctl enable httpd
    sudo systemctl start httpd
    ```
   - ![Apache Server](./images/Capture-ApacheLive.JPG)
   - 
### Install PHP and it’s depemdencies
    ```
    sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
    sudo yum module list php
    sudo yum module reset php
    sudo yum module enable php:remi-7.4
    sudo yum install php php-opcache php-gd php-curl php-mysqlnd
    sudo systemctl start php-fpm
    sudo systemctl enable php-fpm
    setsebool -P httpd_execmem 1
    ```
   - ![Install and configure PHP](./images/Capture-InstallConfigurePHP.JPG)

### Download wordpress and copy wordpress to var/www/html
	```
	mkdir wordpress
	cd   wordpress
	sudo wget http://wordpress.org/latest.tar.gz
	sudo tar xzvf latest.tar.gz
	sudo rm -rf latest.tar.gz
	cp wordpress/wp-config-sample.php wordpress/wp-config.php
	cp -R wordpress /var/www/html/
	```
### Configure DB to work with WordPress
	```
	sudo mysql
	CREATE DATABASE wordpress;
	CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
	GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
	FLUSH PRIVILEGES;
	SHOW DATABASES;
	exit
	```
### Connect to Mysql Server from WebServer
	```
	sudo yum install mysql
	sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
	```
   - ![Connected WebServer - DB Server](./images/Capture-ConnectedWebServer&DBServer.JPG) 
   - ![LocalHost Server Running](./images/Capture-ConfigureWordPress.JPG)
   - ![LocalHost Server Running](./images/Capture-wordpressInstalled.JPG)
    
