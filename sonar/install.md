![554521](https://github.com/user-attachments/assets/ca65b6ae-7334-4e20-bc8a-d5d870643fad)
#    **Sonarqube Installation**

| **Author**   | **Created on** | **Version** | **Last updated by** | **Last edited on** | **Level** | **Reviewer**  | 
|--------------|----------------|-------------|---------------------|--------------------|-----------|---------------|
|      |         | v1 |          |      |        |   |  
| Rohit Chopra      |   27-02-25      | v1 | Rohit Chopra         |     27-02-25  |     Internal     | Siddharth Pawar, Komal Jaiswal  |  

___

## **Table of Contents**  
## Table of Contents

- [Purpose of Document](#purpose)
- [Introduction](#introduction)
- [Pre-requisites](#pre-requisites)
- [System Requirements](#system-requirements)
- [Important Ports](#important-ports)
- [Step-by-step installation](#step-by-step-installation)
    - [Step 1. Update and Upgrade System Packages](#step-1-update-and-upgrade-system-packages)
    - [Step 2. Java installation](#step-2-java-installation)
    - [Step 3. Verify the installed Java version](#step-3-verify-the-installed-java-version)
    - [Step 4. Download and Install SonarQube](#step-4-download-and-install-sonarqube)
        - [1. Install the zip utility](#1-install-the-zip-utility)
        - [2. installing the latest version of SonarQube 10.4 Community Edition](#2-installing-the-latest-version-of-sonarqube-104-community-edition)
        - [3. Move the unzipped files to the /opt/sonarqube directory](#3-move-the-unzipped-files-to-the-optsonarqube-directory)
    - [Step 5. Configure SonarQube](#step-5-configure-sonarqube)
    - [Step 6. Check the service status](#step-6-check-the-service-status)
- [Contact Information](#contact-information)  
- [References](#references)  

# **SonarQube Installation Guide**

## **Purpose**
This document provides a step-by-step guide to installing and configuring SonarQube 10.4 Community Edition on a Linux-based system. It includes pre-requisites, system requirements, important ports, and detailed installation steps.

---

## **Introduction**
SonarQube is an open-source platform for continuous inspection of code quality. It performs static code analysis to detect bugs, code smells, and security vulnerabilities in over 20 programming languages. This guide will help you install and configure SonarQube on a Linux system.

---
## **Pre-requisites**
Before proceeding with the installation, ensure the following:

| Pre-requisite | Details |
|---|---|
| Java Development Kit (JDK) 17 | Installed |
| PostgreSQL | Installed and configured (version 12 or higher) |

---
## **System Requirements**
| **Specification**      | **Details**         |
|-------------------------|---------------------|
| **Operating System**    | Ubuntu 22.04      |
| **CPU**                | 2 vCPU             |
| **Hard Disk**             | 20 GB              |
| **RAM**                | 4 GB               |

---
## **Important Ports**

| **Port** | **Protocol** | **Use Case**       |
|----------|--------------|--------------------|      
| 9200      | TCP          |    Elasticsearch (used internally by SonarQube)             |             
| 5432     | TCP          | PostgreSQL database access  |    
| 9000     | TCP          |      SonarQube Web Interface        |    

---
# **Step-by-step installation**

## **Step 1. Update and Upgrade System Packages**

``` bash
sudo apt update
```

``` bash
sudo apt upgrade -y
```

## **Step 2. Java installation**

``` bash
sudo apt install -y openjdk-17-jdk
```


##  **Step 3. Verify the installed Java version**

``` bash
java -version
```
![java_version](https://github.com/user-attachments/assets/c25ac5d8-03d7-495f-a941-c1b3d07f0f4c)

___

## **Step 4. Download and Install SonarQube**

#### 1. **Install the zip utility**

``` bash
sudo apt install zip -y
```

#### 2. **Installing the latest version of SonarQube 10.4 Community Edition**

``` bash
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.0.0.68432.zip

```

#### 3. **Move the unzipped files to the /opt/sonarqube directory**

``` bash
sudo unzip sonarqube-10.0.0.68432.zip
sudo mv sonarqube-10.0.0.68432 sonarqube
sudo mv sonarqube /opt/
```

__

## **Step 5. Configure SonarQube**

To configure the sonarqube follow the [link to configure Sonarqube]( https://github.com/snaatak-Zero-Downtime-Crew/Documentation/blob/Rohit-SCRUM-77/Common/Software%20/Sonarqube/Configuration/README.md)

## **Step 6. Check the service status**

``` bash
sudo systemctl status sonar
```

## **Contact Information**

| Name| Email Address      | GitHub | URL |
|-----|--------------------------|----------|---------|
| Rohit Chopra | rohit.chopra@mygurukulam.co|  rohitchopra-mygurukulam  |  https://github.com/rohitchopra-mygurukulam  |
---


## **References**

| **Link** | **Description** |
|------------------------------------------------------|------------------|
| [Sonarqube](https://www.sonarsource.com/learn/static-code-analysis-using-sonarqube/)          | Static Code Analysis Using SonarQube    |
| [Sonarqube Installation Guide](https://www.digitalocean.com/community/tutorials/how-to-ensure-code-quality-with-sonarqube-on-ubuntu-18-04)          | Steps for SonarQube installation    |
