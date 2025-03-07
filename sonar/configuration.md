#    **Sonarqube Configuration Guide**


| **Author**   | **Created on** | **Version** | **Last updated by** | **Last edited on** | **Level** | **Reviewer**  | 
|--------------|----------------|-------------|---------------------|--------------------|-----------|---------------|
|      |         | v1 |          |      |        |   |  
| Rohit Chopra      |   27-02-25      | v1 | Rohit Chopra         |     27-02-25  |     Internal     |  Komal Jaiswal  |  

___
# Table of Contents

1.  **[Step 1. Add SonarQube Group and User](#step-1-add-sonarqube-group-and-user)**
2.  **[Step 2. Configure SonarQube](#step-2-configure-sonarqube)**
3.  **[Step 3. Edit the SonarQube script file](#step-3-edit-the-sonarqube-script-file)**
4.  **[Step 4. Add RUN_AS_USER](#step-4-add-run_as_user)**
5.  **[Step 5. Setup Systemd service](#step-5-setup-systemd-service)**
6.  **[Step 6. Modify Kernel System Limits](#step-6-modify-kernel-system-limits)**
7.  **[Step 7. Access SonarQube Web Interface](#step-7-access-sonarqube-web-interface)**
___
## **Step 1. Add SonarQube Group and User**

Create a dedicated user and group for SonarQube, which can not run as the root user.

**Note:** You can give any name for the sonar user and group. I have here given the user and group name to be the same i.e sonar.

#### **1.Create a sonar group.**

```bash
sudo groupadd sonar
```
#### **2.Create a sonar user and set /opt/sonarqube as the home directory.**

``` bash
sudo useradd -d /opt/sonarqube -g sonar sonar
```

#### **3.Grant the sonar user access to the /opt/sonarqube directory.**
``` bash
sudo chown sonar:sonar /opt/sonarqube -R
```

___
## **Step 2. Configure SonarQube**

#### 1. Edit the SonarQube configuration file.

``` bash
sudo nano /opt/sonarqube/conf/sonar.properties
```


#### 2. Uncomment the following lines in the SonarQube configuration file**

``` bash
sonar.jdbc.username=sonar
sonar.jdbc.password=abc@12345
```

``` bash
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sqube
```

![userpw](https://github.com/user-attachments/assets/dcd8a3fe-c00d-4329-a47d-854a27b05666)

## **Step 3. Edit the SonarQube script file**

``` bash
sudo nano /opt/sonarqube/bin/linux-x86-64/sonar.sh
```
## **Step 4. Add Run_AS_USER**

``` bash
RUN_AS_USER=sonar

```
![script](https://github.com/user-attachments/assets/6d4e71a6-ecbf-4eb3-ab04-3adeff933f29)

## **Step 5. Setup Systemd service**

#### 1. **Create a new service file using a text editor**

``` bash
sudo nano /etc/systemd/system/sonar.service
```


#### 2. **Paste the following lines to the file.**

``` bash
[Unit]
Description=SonarQube service
After=syslog.target network.target
[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
User=sonar
Group=sonar
Restart=always
LimitNOFILE=65536
LimitNPROC=4096
[Install]
WantedBy=multi-user.target

```

**Note:** Here in the above script, make sure to change the User and Group section with the value that you have created. For me its:

User=**sonar**

Group=**sonar**



#### 3. **Enable the SonarQube service to start at boot**

``` bash
sudo systemctl enable sonar
```


#### 4. **start the SonarQube service**

``` bash
sudo systemctl start sonar
```
#### 5. **Check the service status**
``` bash
sudo systemctl status sonar
```

![Status](https://github.com/user-attachments/assets/94cacbd4-d04e-4872-8e79-a8b0b75b1874)

___
## **Step 6. Modify Kernel System Limits**

#### 1. **Edit the sysctl configuration file.**
``` bash
sudo nano /etc/sysctl.conf
```


#### 2. **Add the following lines to the sysctl configuration file (/etc/sysctl.conf)**

``` bash
vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
```
- **vm.max_map_count** defines the maximum number of memory map areas a process can have.
- **fs.file-max** defines the maximum number of file handles that the kernel can allocate.
- **ulimit -n** sets the maximum number of open file descriptors that a process can have.


#### 3.**To apply the changes, reload daemon**
``` bash
sudo systemctl daemon-reload
```
___
## **Step 7. Access SonarQube Web Interface**


#### 1. Access SonarQube in a web browser at your server’s PublicIP address on port 9000.

``` bash
http://32.26.240.39:9000
```
**Note:** the default username and password are admin and admin respectively.
![image](https://github.com/user-attachments/assets/861bb0fb-5295-4fc2-9670-d6c1395a2fcd)

#### 2. Change the Old password with a New one.

Once logged in, Sonarqube will prompt you to change your password. Enter the current password “admin” and then enter your new password twice as prompted.

___

## **Contact Information**

| Name| Email Address      | GitHub | URL |
|-----|--------------------------|----------|---------|
| Rohit Chopra | rohit.chopra@mygurukulam.co|  rohitchopra-mygurukulam  |  https://github.com/rohitchopra-mygurukulam  |
---


## **References**

| **Link** | **Description** |
|------------------------------------------------------|------------------|
| [Sonarqube Configuration Guide](https://www.digitalocean.com/community/tutorials/how-to-ensure-code-quality-with-sonarqube-on-ubuntu-18-04)          | Steps for SonarQube installation    |
