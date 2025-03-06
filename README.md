How To Install Apache Cloud Stack Management Server on Ubuntu 22.04

Update your Ubuntu system before proceeding with further steps. 
Run the following commands in your terminal: 
sudo apt update – Refresh the list of available packages and their versions.
sudo apt-get upgrade -y – Upgrade all installed packages to their latest versions.

STEP 1 
Commond -> ip a

STEP-2
Commond –> ip r


STEP-3
Commond - > cd /etc/netplan/
ls
cat /etc/netplan/01-network-manager-all.yaml (chaing the ip to static)

STEP-4
Commond - sudo nano /etc/netplan/01-network-manager-all.yaml
 For reference go to this link https://www.freecodecamp.org/news/setting-a-static-ip-in-ubuntu-linux-ip-address-tutorial/
 

network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:
      dhcp4: no
      addresses: [192.168.49.130/24]
      routes:
        - to: default
          via: 192.168.49.2
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
 

Step-5 
Commomd->sudo netplan apply

Step-6
Commond-> sudo systemctl restart NetworkManager

Step-7
Commond-> hostname –fqdn

STEP-8
Commond->sudo nano /etc/hosts

127.0.0.1       localhost
127.0.1.1       mukesh1
192.168.49.130 apache.cloud.u1.cloud           (Add Your ip here)

 

Step-9
Commond->sudo hostnamectl set-hostname cloud

STEP-10
Commond->hostname –fqdn

Step 11: sudo apt install bridge-utils
Step 12: sudo brctl addbr cloudbr0
Step 13: sudo brctl addif cloudbr0 ens33
Step 14: sudo nano /etc/netplan/01-network-manager-all.yaml
For reference go to this link-- https://www.inf.ufpr.br/jwvflauzino/vines/installation-guide/ubuntu-18.04-all-in-one.html
Step 15: sudo netplan apply
Step 16: sudo systemctl restart NetworkManager
Step 17: sudo apt install ntp
Step 18: sudo systemctl enable ntp
Step 19: sudo systemctl start ntp
Step 20: sudo apt install chrony
Step 21: sudo apt install openjdk-11-jdk
Step 22: sudo nano /etc/apt/sources.list.d/cloudstack.list
For reference go to this link--  https://docs.cloudstack.apache.org/en/latest/installguide/management-server/

Open the file and add: deb https://download.cloudstack.org/ubuntu focal 4.18
 


Step 23: add public key to the trusted keys
wget -O - https://download.cloudstack.org/release.asc |sudo tee/etc/apt/trusted.gpg.d/cloudstack.asc

Step 24: update local apt cache: sudo apt update

Error coming: use below commands
sudo apt install --only-upgrade ca-certificates
[trusted = yes]


Step 25: sudo apt install cloudstack-management
Step 26: sudo apt install mysql-server

Step 27: open file /etc/mysql/my.cnf 
sudo nano /etc/mysql/my.cnf
add the below lines at the bottom:

[mysqld]
server-id=1
innodb_rollback_on_timeout=1
innodb_lock_wait_timeout=600
max_connections=350
log-bin=mysql-bin
binlog-format = 'ROW'
 


Step 28: sudo systemctl restart mysql
Step 29: sudo mysql_secure_installation



Step 30: sudo mysql
add one by one
-- Create the cloud and cloud_usage databases
CREATE DATABASE `cloud`;
CREATE DATABASE `cloud_usage`;
-- Create the cloud user
CREATE USER cloud@`localhost` identified by '<password>';
CREATE USER cloud@'localhost' identified by '123@Msql';
CREATE USER cloud@`%` identified by '<password>';
CREATE USER cloud@'%' identified by '1234@Sql';
-- Grant all privileges to the cloud user on the databases
GRANT ALL ON cloud.* to cloud@`localhost`;
GRANT ALL ON cloud.* to cloud@`%`;
GRANT ALL ON cloud_usage.* to cloud@`localhost`;
GRANT ALL ON cloud_usage.* to cloud@`%`;
-- Grant process list privilege for all other databases
GRANT process ON *.* TO cloud@`localhost`;
GRANT process ON *.* TO cloud@`%`;
Exit



Step 31: sudo cloudstack-setup-databases cloud:password@localhost --deploy-as=root


Step 34: sudo mkdir -p /export/primary
Step 35: sudo mkdir -p /export/secondary

Step 36: sudo nano /etc/exports

Insert the line: /export *(rw,async,no_root_squash,no_subtree_check)
 

Step 37: sudo apt install nfs-kernel-server

Step 38: sudo mkdir -p /mnt/primary /mnt/secondary

Step 38: sudo mkdir -p /mnt/primary /mnt/secondary

Step 39: sudo echo "192.168.11.159:/export/primary /mnt/primary nfs rsize=8192,wsize=8192,timeo=14,intr,vers=3,noauto 0 2" >> /etc/fstab
bash: /etc/fstab: Permission denied

Step 40: sudo chmod 777 /etc/fstab

Step 41: sudo echo "192.168.11.159:/export/primary /mnt/primary nfs rsize=8192,wsize=8192,timeo=14,intr,vers=3,noauto 0 2" >> /etc/fstab

Step 42: sudo echo "192.168.11.159:/export/secondary /mnt/secondary nfs rsize=8192,wsize=8192,timeo=14,intr,vers=3,noauto 0 2" >> /etc/fstab

Step 43: sudo mount /mnt/primary

Step 44: sudo mount /mnt/secondary

Step 45: Open the Browser and type the url: http://192.168.11.159:8080/      ADD here your ip 

The following Page will open:
Step 46: Provide the default Credentials:
Username: admin, Password: password
The following Page will open
