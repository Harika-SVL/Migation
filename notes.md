### Story of OrganicIndiaCorp

* This organization runs an ecommerce application for selling organic fmcg
* This organization has datacenter in hyderabad

![alt text](shots/1.PNG)

### Problems

* From the 1 – 5 of every month orders increase. Right now OrganicIndiaCorp has 16 vms where they are running backend services. Inspite of this from 1-5 customers are facing latency issues and slow responses
* Right now this organization is running only in one data center which is a single point of failure, so they have one more datacenter with same number of servers in noida.
* So, architect of OrganicIndiaCorp has suggested to move to AWS

### How can AWS Solve these problems

* Lets understand basic merits of cloud (AWS)
    * Global presence
    * Elasticity: Automatically increasing and decreasing the resources based on some dynamic metrics
    * Disaster Recovery is easier to setup
    * Pay as you go.

### Service Models: IaaS vs PaaS vs SaaS

![alt text](shots/2.PNG)

### How to Move to AWS

* Migration: i.e. move the applications/data on your physical servers into AWS
* Migration based on workloads is classified into 3 types
    * Server Migration
    * Database Migration
    * Storage Migration

### Self-learning (3 – days)

* Grooming Sessions: 

    [ Refer Here : https://www.youtube.com/watch?v=6hRzUNLmZ0E&list=PLuVH8Jaq3mLsYeR_xaW0RW1KR9BZKFBT4 ]

* Cloud Essentials: 

    [ Refer Here : https://www.youtube.com/watch?v=nd1a0bTlqNI&list=PLuVH8Jaq3mLuwOIlTbyr86O2GYS6mPvM9 ]

### OrganicIndiaCorp Application Architecture

![alt text](shots/3.PNG)

### Order of Migration

* Always from least dependent to most dependent
* In this case
    * Storage Layer and DB Layer are least dependent
    * Application Layer
    * Presentation Layer (Most Dependent)

### Migration Strategies

* 7R’s of migration
    * Re-Host: Lift and shift

    ![alt text](shots/4.PNG)

    * Re-platform: Lift tinker and shift

    ![alt text](shots/5.PNG)

    * Re-factor/Re-architect: Re-write the complete application in a modern cloud native way.
    * Retain
    * Retire
    * Re-purchase
    * Re-locate

    ![alt text](shots/6.PNG)

### Terms

* Hypervisor
* Greenfield and Brownfield
* P2V and V2V

### Exercise

* What are different types of hypervisors?

### Hypervisor Types

* Hypervisors are of two types
    * Type I hypervisor

    ![alt text](shots/7.PNG)

    * Type II hypervisor

    ![alt text](shots/8.PNG)

* Virtualization

![alt text](shots/9.PNG)

### What does Migration mean in the context of server

* Contents of the disk are replicated to AWS which will eventually create Amazon Machine Images (AMI)

![alt text](shots/10.PNG)

* We will be migrating unique application hosted servers to AWS

### Migration approaches

* We have two approaches
    * One-time migration/replication
    * On-going replication/migration

### Terms

* AWS Partner network

### What is supported ways by AWS Migration ?

* Agent vs Agentless

Preview

### Assumptions

* We are not seeking help from partner network
* We are not using any third party applications, we are using AWS tools

### Migration Steps

* In AWS We are going to perform
    * P2V
    * V2V

### Simulating On-premises

* Since we dont have physical infra, we will be simulating this on cloud (AWS/Azure)

![alt text](shots/11.PNG)

### Migration Steps

* Migration workflow Refer Here
* Migration limits Refer Here
* Supported OS Refer Here
* Steps

![alt text](shots/12.PNG)

### Source Environment Prepartion

* For this activity we will be installing nop commerce
* overview of architecture

![alt text](shots/13.PNG)

* Create two
    * Azure ubuntu linux vms of size standard_b1s
    * AWS ubuntu linux instances of size t2.micro or t3.micro
* Consider one vm to be database server and install mysql
    * Steps: 
    
    [ Refer Here : https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04 ]

    ```
    sudo apt update
    sudo apt install mysql-server
    sudo mysql
    ```
    * In the mysql shell try to execute following commands
    ```
    CREATE USER 'nop'@'localhost' IDENTIFIED BY 'nop12345';
    GRANT ALL PRIVILEGES ON *.* TO 'nop'@'localhost';
    FLUSH PRIVILEGES;
    exit
    ```
    * To verify the nop execute mysql -u nop -p enter password and you should be allowed in sql shell



    * Note: we need to fix the issue with external connectivity
* Application: nopcommerce 

    [ Refer Here : https://docs.nopcommerce.com/en/installation-and-upgrading/installing-nopcommerce/installing-on-linux.html ]

* Steps:
    * Install .net core 7 
    
    [ Refer Here : https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu-install?pivots=os-linux-ubuntu-2004&tabs=dotnet8 ]

    * download nopcommerce zip
    ```
     sudo apt install unzip -y 
    ```
    [ Refer here : https://docs.nopcommerce.com/en/installation-and-upgrading/installing-nopcommerce/installing-on-linux.html ]

    ```
    sudo mkdir /usr/share/nopCommerce
    cd /usr/share/nopCommerce/
    sudo wget https://github.com/nopSolutions/nopCommerce/releases/download/release-4.60.3/nopCommerce_4.60.3_NoSource_linux_x64.zip
    sudo unzip nopCommerce_4.60.3_NoSource_linux_x64.zip
    sudo mkdir bin
    sudo mkdir logs 
    ```
* Create a user called as nop
```
sudo useradd nop
```
* Give full permissions to `/usr/share/nopCommerce` to nop
```
sudo chgrp -R nop /usr/share/nopCommerce/
sudo chown -R nop /usr/share/nopCommerce/
```
* Create a file in `/etc/systemd/system/nopCommerce.service` with following content
```
[Unit]
Description=Example nopCommerce app running on Xubuntu

[Service]
WorkingDirectory=/usr/share/nopCommerce
ExecStart=/usr/bin/dotnet /usr/share/nopCommerce/Nop.Web.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=nopCommerce-example
User=nop
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=ASPNETCORE_URLS=http://0.0.0.0:5000
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```
* Now execute
```
sudo systemctl enable nopCommerce.service
sudo systemctl start nopCommerce.service
sudo systemctl status nopCommerce.service
```
* Now access the application using http://:5000



* Enabling mysql connections from anywhere 

    [ Refer Here : https://www.techrepublic.com/article/create-mysql-8-database-user-remote-access-databases/ ]

* Step 1:
```
The first thing we must do is configure MySQL for remote connections. To do this, log into your MySQL database server and open the configuration file with the command:

sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

In that file, look for the line:

bind-address = 127.0.0.1

Change that line to:

bind-address = 0.0.0.0

Save and close the file. Restart the MySQL service with:

sudo systemctl restart mysql
```
* Step 2: launch mysql shell `sudo mysql`
```
CREATE USER 'nop'@'%' IDENTIFIED BY 'nop12345';
GRANT ALL PRIVILEGES ON *.* to 'nop'@'%';
FLUSH PRIVILEGES;
exit
```
* Lets configure ecommerce application in webpage `http://<ip>:5000`
* Enter database details and credentials `nop/nop12345`
* wait for few minutes for the page to be reloaded



### AWS Migration P2V

* Refer Here for the docs
Refer Here for the first time setup guide
Steps