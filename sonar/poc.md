# **POC of configuration using ansible tool**

![image](https://github.com/user-attachments/assets/9354a8ce-9689-43a7-99b1-2c391326ebe2)


| **Author** | **Created on** | **Version** | **Last updated by**|**Last Edited On**|**Level** |**Reviewer** |
|------------|---------------------------|-------------|----------------|-----|-------------|-------------|
| Aayush Verma|   01-03-2025              | v1.1       | Aayush Verma   | 03-03-2025   |  L0 | Akshit Kapil |
| Aayush Verma|   01-03-2025              | v1         | Aayush Verma   | 01-03-2025   |  Internal Reviewer | Siddharth |


## **Table of Contents**

<details>
  <summary>Step-by-step installation </summary>

  1. [Install Ansible](#step-1-install-ansible)
  2. [Dynamic Inventory Setup](#step-2-dynamic-inventory-setup)
  3. [AWS EC2 Inventory](#step-3--aws-ec2-inventory)
  4. [Create Ansible Role](#step-4-create-ansible-role)  
  5. [SonarQube Playbook](#step-5-sonarqubeyml-playbook)
  6. [Importent File](#step-6-importent-file)
        - [ Main.yml File ](#61-mainyml)
        - [ Defaults Variables File ](#62-defaults-variables)
        - [ Vars Variables File ](#63-vars-variables)
        - [ Subtasks File ](#64-subtasks-file)
  7. [Templates for configuration](#step-7-templates-for-configuration)  
        - [ SonarQube Configure File ](#71-sonarqube-configure-file-)
        - [ SonarQube service File ](#72-sonarqube-service-file-)
  8. [Playbook Execution](#step-8-playbook-execution) 
  9. [Post Installation setup](#post-installation-setup)
  10. [Conclusion](#conclusion)
  11. [Contact Information](#contact-information)

</details>


## Step-by-step installation 

### **Step 1: Install Ansible**

 To install Ansible on your system, please follow the link below for the Ansible Installation Guide. :- [Ansible Installation Guide](https://github.com/snaatak-Zero-Downtime-Crew/Documentation/tree/Aayush-SCRUM-93/Common/Software/Ansible/Installation)


### **Step 2: Dynamic Inventory Setup** 

```yaml

[defaults]

# Sets the default inventory file to use, which is the AWS EC2 dynamic inventory file.
inventory = ./aws_ec2.yml

# Specifies the private key file used for SSH authentication when connecting to remote hosts.
private_key_file = /path/to/private_key

# Disables SSH host key checking to avoid prompts for unknown hosts.
host_key_checking = False

# Defines the number of parallel forks (simultaneous tasks) Ansible can run.
forks = 10

# Enables the AWS EC2 dynamic inventory plugin to fetch instance details dynamically.
enable_plugins = aws_ec2

# Specifies the default remote user for SSH connections.
remote_user = ubuntu

[inventory]

# Enables the use of inventory plugins, specifically AWS EC2.
enable_plugins = aws_ec2

```
[Ansible config file link](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/ansible.cfg)

> [!NOTE]
>* Ensure that for dynamic inventory you have the necessary AWS credentials configured in [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) or an IAM role on the node. 
>* Change and modify values according to your needs and purpose.

---

### **Step 3:  AWS EC2 Inventory**

```yaml
---
plugin: aws_ec2
regions:
  - "us-east-1"
filters:
  tag:Type:
    - "sonarQube"
  instance-state-name : running
compose:
  ansible_host: private_ip_address
```

[SonarQube Dynamic Inventory link](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/aws_ec2.yml)


1. `plugin: aws_ec2`: Specifies the use of the aws_ec2 plugin as the dynamic inventory source. This plugin is designed to fetch information about EC2 instances in AWS.
2. `regions: - us-east-1`: Indicates the AWS region(s) from which the dynamic inventory should fetch information.
3. `Server: "'sonarQube'`: Creates an Ansible group named sonar. This group includes EC2 instances where the tag named Type has a value of 'sonar'. You can tag all your sonarqube instances accordingly.
4. `instance-state-name: running`: Filters the inventory to include only running EC2 instances.  
5. `compose: ansible_host: private_ip_address`: Sets `ansible_host` to the instance's private IP for SSH access.

---

### **Step 4: Create Ansible Role**
* Create a new Ansible role which should follow this directory structure:

<img width="1019" alt="image" src="https://github.com/user-attachments/assets/62c9f245-9214-42c2-b58a-66a789f13730" />


[SonarQube Ansible Role link](https://github.com/snaatak-Zero-Downtime-Crew/ansible/tree/Aayush-SCRUM-93/Sonarqube/sonarQube-setup)

[Create Role link](https://github.com/snaatak-Zero-Downtime-Crew/Documentation/blob/Aayush-SCRUM-93/Common/Software/Ansible/Installation/README.md#step-5-initialize-a-new-ansible-role)

---

### **Step 5: SonarQube.yml Playbook**
* This file is defining a set of tasks to be executed on hosts belonging to the ubuntu group.

```yaml
---
- hosts: all
  become: yes
  roles:
    - sonarQube-setup
```

[SonarQube Playbook link](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/SonarQube.yml)

---

###  **Step 6: Importent File**

#### **6.1. main.yml:** 
- This main.yml file is acting as an orchestrator, importing tasks from the `dependence.yml` , `useranddb.yml` & `sonarqube.yml` files. This separation of tasks into different files is a good practice for better organization, especially when dealing with complex configurations or roles.

```yaml
---
# tasks file for sonarQube-setup
- name: install all dependence
  include_tasks: dependence.yml
  when: ansible_facts['os_family'] == 'Debian'
- name: postgresql user 
  include_tasks: useranddb.yml
  when: ansible_facts['os_family'] == 'Debian'
- name: Install SonarQube
  include_tasks: sonarqube.yml
  when: ansible_facts['os_family'] == 'Debian'
```

[SonarQube Tasks link](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/tasks/main.yml)

---

#### **6.2. Defaults variables:** 
- This role comes with default values for several variables that have been used in the role. You can find these defaults in the `defaults/main.yml` file within the role directory.

```yaml
---
# defaults file for sonarQube-setup
postgresql_version: "16"
jdk_version: "17"
sonarqube_version: "10.0.0.68432"
sonarqube_web_port: 9000
sonarqube_user: "ddsonar"
sonarqube_group: "ddsonar"
sonarqube_db: "ddsonarqube"
sonarqube_db_user: "ddsonar"
sonarqube_db_password: "mwd#2%#!!#%rgs"
```

### Role Defaults Variables 

| **Variable**            | **Value**                | **Description**                                |  
|-------------------------|-------------------------|------------------------------------------------|  
| `postgresql_version`    | `"16"`                   | PostgreSQL version for SonarQube database.     |  
| `jdk_version`          | `"17"`                   | Java Development Kit version required.         |  
| `sonarqube_version`    | `"10.0.0.68432"`         | SonarQube version to be installed.             | 
| `sonarqube_web_port`   |  `"9000"`                | SonarQube webserver port |
| `sonarqube_user`       | `"ddsonar"`              | User for running SonarQube.                    |  
| `sonarqube_group`      | `"ddsonar"`              | Group for SonarQube user.                      |  
| `sonarqube_db`         | `"ddsonarqube"`          | Database name for SonarQube.                   |  
| `sonarqube_db_user`    | `"ddsonar"`              | Database user for SonarQube.                   |  
| `sonarqube_db_password` | `"mwd#2%#!!#%rgs"`     | Password for the SonarQube database user.      |  


[SonarQube Defaults Variables link](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/defaults/main.yml)

> [!NOTE]
> To customize the SonarQube installation based on your specific requirements, you can override these default values in main.yaml file in the vars directory of the role. 

---

#### **6.3 vars variables:** 
- This role comes with static values for several variables that are defined in the `vars/main.yml` file within the role directory. 

```yaml
---
# vars file for sonarQube-setup
sonarqube_install_dir: "/opt/sonarqube-{{ sonarqube_version }}"
sonarqube_service_name: "sonarqube"
sonarqube_download_url: "https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-{{ sonarqube_version }}.zip"
sysctl_config:
      - "vm.max_map_count=262144"
      - "fs.file-max=65536"
      - "ulimit -n 65536"
      - "ulimit -u 4096"
required_packages:
  - openjdk-{{ jdk_version }}-jdk
  - postgresql
  - postgresql-contrib
  - python3-psycopg2
  - zip
```

### Role Vars/static Variables 


| **Variable**                 | **Description** |  
|------------------------------|---------------|  
| `sonarqube_install_dir`      | Installation directory for SonarQube, dynamically set based on the version. |  
| `sonarqube_service_name`     | The system service name for SonarQube. |  
| `sonarqube_download_url`     | URL to download the specified SonarQube version. |  
| `sonarqube_web_port`         | The port on which SonarQube will run (default: 9000). |  
| `sysctl_config`              | System and user limits required for SonarQube. |  
| `vm.max_map_count=262144`    | Sets the maximum number of memory map areas a process can use. |  
| `fs.file-max=65536`          | Sets the maximum number of open file descriptors. |  
| `ulimit -n 65536`            | Sets the maximum number of open file descriptors per process. |  
| `ulimit -u 4096`             | Sets the maximum number of user processes. |  
| `required_packages`          | List of necessary system packages for SonarQube installation. |  
| `openjdk-{{ jdk_version }}-jdk` | Java Development Kit required for SonarQube. |  
| `postgresql`                | PostgreSQL database for SonarQube. |  
| `postgresql-contrib`        | Additional PostgreSQL utilities. |  
| `python3-psycopg2`          | Python package to interact with PostgreSQL. |  
| `zip`                       | Utility for handling compressed files. |  


[SonarQube vars/Static Variables link](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/vars/main.yml)

> [!NOTE]
> These variables typically have higher precedence than those in the `defaults/main.yml` file.

---

#### **6.4 Subtasks File:** 
- This file is included in the `dependence.yml`, `useranddb.yml` & `sonarqube.yml` files.

    1. [dependence.yml](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/tasks/dependence.yml):- This Ansible playbook updates the package cache, installs required packages (using a variable required_packages), and ensures the PostgreSQL service is running and enabled at boot. 
  
    2. [useranddb.yml](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/tasks/useranddb.yml):-This Ansible playbook ensures a PostgreSQL user and database for SonarQube exist. It creates the user if not present, updates the password, creates the database if missing, and grants full privileges to the user. 

    3. [sonarqube.yml](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/tasks/sonarqube.yml):-This Ansible playbook installs and configures SonarQube by downloading and extracting it, creating a dedicated user and group, setting permissions, configuring SonarQube using templates, setting up a systemd service, and updating sysctl settings.

---

### **Step 7: Templates for Configuration**

We need to create two jinja2 templates :

* To configure [SonarQube configure](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/templates/sonarqube.conf.j2)
* To set up [SonarQube Service](https://github.com/snaatak-Zero-Downtime-Crew/ansible/blob/Aayush-SCRUM-93/Sonarqube/sonarQube-setup/templates/sonarqube.service.j2)

#### **7.1 SonarQube configure File:-** 

- `sonarqube.conf.j2` teamplate includes parameteters to configure SonarQube database and webserver

```yaml
# SonarQube configuration file

sonar.jdbc.url=jdbc:postgresql://localhost:5432/{{ sonarqube_db }}
sonar.jdbc.username={{ sonarqube_db_user }}
sonar.jdbc.password={{ sonarqube_db_password }}

sonar.web.port={{ sonarqube_web_port }}
```

#### **7.2 SonarQube Service File:-** 

- `sonarqube.service.j2` template creates a service file for setting up `sonarqube.service`
```ini
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
ExecStart={{ sonarqube_install_dir }}/bin/linux-x86-64/sonar.sh start
ExecStop={{ sonarqube_install_dir }}/bin/linux-x86-64/sonar.sh stop
User={{ sonarqube_user }}
Group={{ sonarqube_group }}
Restart=always
LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target
```

---

### **Step 8: Playbook Execution**

* To set up Jenkins on your target servers, you will execute the Ansible playbook using the following command:

```bash
ansible-playbook -i aws_ec2.yml SonarQube.yml
```

Playbook Execution output:-

<img width="1184" alt="Screenshot 2025-03-01 at 11 04 34 PM" src="https://github.com/user-attachments/assets/8f4d7d4e-2f20-4f6e-ace9-023c339ecffc" />
<img width="1184" alt="Screenshot 2025-03-01 at 11 04 51 PM" src="https://github.com/user-attachments/assets/31607d18-5d64-4f01-8f45-ee17bb60835f" />




> Additional Options
> 
> --limit: You can use this option to specify a subset of hosts from the inventory on which the playbook should be executed.
> 
> -e or --extra-vars: You can pass extra variables to the playbook using this option.

---


## Output
1.  **Host-level output**: Output for each host would indicate whether the playbook execution was successful or not.

<img width="1196" alt="Screenshot 2025-03-01 at 11 06 52 PM" src="https://github.com/user-attachments/assets/64994578-7c30-4793-ade5-e2cba7790ce9" />

***

## Post-Installation Setup
* Once the playbook is executed successfully, Open your web browser and SonarQube UI can be accessed at`http://sonarqube-server-ip:9000`.

  <img width="1440" alt="Screenshot 2025-03-01 at 11 08 31 PM" src="https://github.com/user-attachments/assets/bb5b3430-b9a2-4ab5-8123-877b80f506ca" />

> [!NOTE]
> Make sure that the server security group allows traffic to default sonarqube port 9000. 


* On your first login post installation, you can log in using: `username: admin` & `password: admin`

<img width="737" alt="Screenshot 2025-03-01 at 11 09 46 PM" src="https://github.com/user-attachments/assets/d0e00a8c-f1b8-4cd7-84fb-2117feddfb7f" />


* You'll be prompted to create a new password

<img width="1440" alt="Screenshot 2025-03-01 at 11 10 14 PM" src="https://github.com/user-attachments/assets/4214e0bb-630b-44e4-8a4d-612778c66191" />


* Once you set your new password. You're all set to begin using SonarQube

<img width="1440" alt="Screenshot 2025-03-01 at 11 11 20 PM" src="https://github.com/user-attachments/assets/aeccab58-f9c9-4f50-8e46-1542883034df" />

## Conclusion

This POC showcases how Ansible simplifies SonarQube deployment on AWS EC2 instances through automation, dynamic inventory, and role-based execution. It ensures scalability, flexibility, and maintainability while enabling seamless CI/CD integration.


## Contact Information


| **Name**       | **Email Address**        |
|----------------|--------------------------|
| Aayush Verma   | <aayush.verma@mygurukulam.co> |

