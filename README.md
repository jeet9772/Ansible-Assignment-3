# Ansible Assignment - 3

## Spring3HibernateApp Infrastructure Setup on AWS

**Author:** Jeetendra Singh

---

## 📌 Objective

The objective of this assignment is to set up the required application infrastructure on an AWS EC2 instance using **Ansible** and deploy the **Spring3HibernateApp** Java application.

The complete deployment process is automated using an Ansible playbook, including:

* Installing JDK 11
* Installing MySQL
* Installing Maven and Git
* Cloning the Spring3HibernateApp repository
* Creating the WAR file using Maven
* Installing Apache Tomcat 7.0.108
* Deploying the WAR file to Tomcat
* Restarting the Tomcat service
* Verifying that the application is running

---

## 📦 Application Information

**Application:** Spring3HibernateApp

**Repository:**
https://github.com/opstree/spring3hibernate

**Website:**
https://opstree.github.io

Spring3HibernateApp is a Java-based application used for testing and demonstration purposes. It uses technologies such as **Spring, Hibernate, MySQL and Tomcat**.

---

## 🛠️ Infrastructure & Tools Used

| Component                | Details               |
| ------------------------ | --------------------- |
| Cloud Provider           | AWS EC2               |
| Configuration Management | Ansible               |
| Operating System         | Linux                 |
| Java                     | OpenJDK 11            |
| Build Tool               | Maven                 |
| Database                 | MySQL                 |
| Application Server       | Apache Tomcat 7.0.108 |
| Source Control           | Git                   |

---

## 📋 Prerequisites

Before running the playbook, make sure:

* AWS EC2 instance is available
* Ansible is installed on the control node
* SSH connectivity is configured
* SSH private key is available
* Target server is added to the Ansible inventory
* Ansible user has sudo privileges
* Target server has internet connectivity

---

# 🔄 Deployment Flow

```text
AWS EC2 Instance
       │
       ▼
Connection Verification
       │
       ▼
Install JDK 11
       │
       ▼
Install MySQL
       │
       ▼
Install Maven & Git
       │
       ▼
Clone Spring3HibernateApp
       │
       ▼
Create WAR File using Maven
       │
       ▼
Install Tomcat 7.0.108
       │
       ▼
Deploy WAR File
       │
       ▼
Restart Tomcat Service
       │
       ▼
Application Running
```

---

# 📸 Task-wise Execution & Screenshots

## 1. Connection Verification

First, Ansible connectivity with the AWS EC2 instance is verified using the following command:

```bash
ansible all -m ping
```

Expected output:

```text
worker1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

### Screenshot

**Add your connection verification screenshot below:**

<img width="1440" height="900" alt="Screenshot 2026-08-27 at 11 54 07 PM" src="https://github.com/user-attachments/assets/3e3b41eb-3e7e-4b3d-8bc5-c7c0f0c1e403" />


---

# 2. Task 1: Install JDK 11

JDK 11 is installed on the target server because the Spring3HibernateApp is a Java-based application.

Ansible task:

```yaml
- name: Install JDK 11
  ansible.builtin.dnf:
    name: java-11-openjdk-devel
    state: present
```

Java installation can be verified using:

```bash
java -version
```

Expected output will show Java 11.

### Screenshot

**Add your JDK 11 installation screenshot below:**

<img width="1440" height="900" alt="Screenshot 2026-08-27 at 11 44 37 PM" src="https://github.com/user-attachments/assets/750c6888-0251-4fb4-8f03-b6161ff75220" />


---

# 3. Task 2: Install MySQL

MySQL Server is installed on the target EC2 instance.

Ansible task:

```yaml
- name: Install MySQL
  ansible.builtin.dnf:
    name: mysql-server
    state: present
```

The MySQL service is then started and enabled:

```yaml
- name: Start and enable MySQL
  ansible.builtin.service:
    name: mysqld
    state: started
    enabled: true
```

MySQL service can be verified using:

```bash
systemctl status mysqld
```

### Screenshot

**Add your MySQL installation screenshot below:**

<img width="1440" height="900" alt="Screenshot 2026-08-27 at 11 57 34 PM" src="https://github.com/user-attachments/assets/8d075488-0ecb-403b-b248-b06def97fd7a" />


---

# 4. Task 3: Create the WAR File Using Maven

The Spring3HibernateApp source code is cloned from GitHub.

Repository:

```text
https://github.com/opstree/spring3hibernate.git
```

The repository is cloned to:

```text
/opt/spring3hibernate
```

Maven is then used to build the application:

```bash
mvn clean package
```

The WAR file is generated inside:

```text
/opt/spring3hibernate/target/
```

The generated WAR can be verified using:

```bash
ls -lh /opt/spring3hibernate/target/
```

### Screenshot

**Add your Maven WAR build screenshot below:**

![Screenshot 2026-08-28 at 12 00 30 AM](https://github.com/user-attachments/assets/d2f5fbf2-fb8d-437e-a596-5ee5e54a97f1)


---

# 5. Task 4: Install Tomcat

Apache Tomcat **7.0.108** is downloaded and extracted on the target server.

Tomcat installation location:

```text
/opt/tomcat/apache-tomcat-7.0.108
```

Tomcat archive:

```text
apache-tomcat-7.0.108.tar.gz
```

Tomcat is configured as a systemd service so that it can be managed using:

```bash
systemctl start tomcat
systemctl stop tomcat
systemctl restart tomcat
systemctl status tomcat
```

### Screenshot

**Add your Tomcat installation screenshot below:**

<img width="1440" height="900" alt="Screenshot 2026-08-28 at 12 02 53 AM" src="https://github.com/user-attachments/assets/e74ffe72-8394-4a70-a668-1a0de81ed427" />


---

# 6. Task 5: Deploy WAR File to Tomcat

After the WAR file is successfully created using Maven, it is copied to the Tomcat `webapps` directory.

Target location:

```text
/opt/tomcat/apache-tomcat-7.0.108/webapps/
```

The WAR file is deployed as:

```text
/opt/tomcat/apache-tomcat-7.0.108/webapps/Spring3HibernateApp.war
```

Example Ansible task:

```yaml
- name: Copy WAR file to Tomcat webapps
  ansible.builtin.copy:
    src: /opt/spring3hibernate/target/Spring3HibernateApp.war
    dest: /opt/tomcat/apache-tomcat-7.0.108/webapps/Spring3HibernateApp.war
    remote_src: true
    mode: '0644'
```

WAR deployment can be verified using:

```bash
ls -lh /opt/tomcat/apache-tomcat-7.0.108/webapps/
```

### Screenshot

**Add your WAR deployment screenshot below:**

<img width="1440" height="900" alt="Screenshot 2026-08-28 at 12 05 52 AM" src="https://github.com/user-attachments/assets/6d760dea-9b89-4756-a7c9-97ccfe8b6fa7" />


---

# 7. Task 6: Restart Tomcat Service

After deploying the WAR file, the Tomcat service is restarted so that Tomcat can load the application.

Command:

```bash
systemctl restart tomcat
```

Verify the Tomcat service:

```bash
systemctl status tomcat
```

The service should show:

```text
Active: active (running)
```

### Screenshot

**Add your Tomcat restart screenshot below:**

<img width="1440" height="900" alt="Screenshot 2026-08-28 at 12 07 19 AM" src="https://github.com/user-attachments/assets/4004389a-88fb-4def-9f1d-49674ae7440c" />


---

# 8. Final Output: Application Running

After successful deployment and Tomcat restart, the Spring3HibernateApp can be accessed through a web browser.

Application URL:

```text
http://<EC2-PUBLIC-IP>:8080/Spring3HibernateApp/
```

Replace `<EC2-PUBLIC-IP>` with the public IP address of your AWS EC2 instance.

The browser output confirms that the Spring3HibernateApp has been successfully deployed and is running.

### Screenshot

**Add your final application running screenshot below:**

<img width="1440" height="900" alt="Screenshot 2026-08-28 at 12 13 22 AM" src="https://github.com/user-attachments/assets/f19918c0-a02f-404d-a16d-aa84b056dd2c" />


---

# 📁 Project Structure

```text
Ansible-Assignment-3/
│
├── inventory
├── spring3hibernate.yml
├── README.md
│
└── screenshots/
    ├── connection-verification.png
    ├── task1-jdk11.png
    ├── task2-mysql.png
    ├── task3-maven-war-build.png
    ├── task4-install-tomcat.png
    ├── task5-deploy-war.png
    ├── task6-restart-tomcat.png
    └── final-output-app-running.png
```

---

# 📝 Inventory Example

Example Ansible inventory:

```ini
[app]
<EC2-PUBLIC-IP> ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/your-key.pem
```

Replace the values according to your AWS EC2 configuration.

---

# ▶️ How to Run

## Step 1: Verify Ansible Connectivity

```bash
ansible all -m ping
```

---

## Step 2: Check Playbook Syntax

```bash
ansible-playbook -i inventory spring3hibernate.yml --syntax-check
```

---

## Step 3: Run the Playbook

```bash
ansible-playbook -i inventory spring3hibernate.yml
```

---

## Step 4: Verify Java

```bash
java -version
```

---

## Step 5: Verify Maven

```bash
mvn -version
```

---

## Step 6: Verify MySQL

```bash
systemctl status mysqld
```

---

## Step 7: Verify Tomcat

```bash
systemctl status tomcat
```

---

## Step 8: Verify WAR Deployment

```bash
ls -lh /opt/tomcat/apache-tomcat-7.0.108/webapps/
```

---

# 🔍 Verification

The following points confirm successful deployment:

* JDK 11 is installed successfully.
* MySQL Server is installed and running.
* Maven and Git are installed.
* Spring3HibernateApp repository is cloned successfully.
* WAR file is created using Maven.
* Apache Tomcat 7.0.108 is installed.
* WAR file is copied to the Tomcat `webapps` directory.
* Tomcat service is restarted successfully.
* Spring3HibernateApp is accessible from the browser.

---

# ⚠️ AWS Security Group

For browser access to the application, make sure the Tomcat port is allowed in the AWS EC2 Security Group.

For example:

```text
Type: Custom TCP
Port: 8080
Source: Your IP / Required Network
```

Then access:

```text
http://<EC2-PUBLIC-IP>:8080/Spring3HibernateApp/
```

---

# 🎯 Expected Result

After successful execution of the Ansible playbook:

```text
JDK 11 Installed
       ↓
MySQL Running
       ↓
Application WAR Created
       ↓
Tomcat Installed
       ↓
WAR Deployed
       ↓
Tomcat Restarted
       ↓
Spring3HibernateApp Running
```

---

# ✅ Conclusion

The **Spring3HibernateApp** infrastructure and deployment process has been successfully automated using **Ansible on AWS EC2**.

The playbook automates the complete deployment process, including Java installation, MySQL setup, Maven WAR creation, Tomcat installation, WAR deployment, Tomcat restart, and application verification.

**Final Result:**
The Spring3HibernateApp is successfully deployed and running on the AWS EC2 instance.

---

## 👨‍💻 Author

**Jeetendra Singh**

**Technology:** Ansible | AWS | Linux | Java | Maven | MySQL | Tomcat
