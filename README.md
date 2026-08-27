# Ansible Assignment - 3: Spring3HibernateApp Infrastructure Setup on AWS

**Author:** Jeetendra Singh

## Objective

The objective of this assignment is to provision and configure a complete application infrastructure on AWS using an Ansible playbook, and deploy the `Spring3HibernateApp` Java application on it in a fully automated manner. The playbook handles everything from installing dependencies to building the application and deploying it on Tomcat.

## About the Application

**Repository:** https://github.com/opstree/spring3hibernate

Spring3HibernateApp is a Java-based application built for various testing purposes, used here to demonstrate an end-to-end Java web application deployment pipeline using Spring, Hibernate, and MySQL.

* **Website:** https://opstree.github.io

## Prerequisites

Before running this playbook, ensure the following are in place:

* An AWS EC2 instance provisioned and reachable via SSH
* Ansible installed on the control node
* SSH key-based access configured to the target host
* The target host added to the Ansible inventory
* `become` (sudo) privileges available for the Ansible user
* Internet access on the target host

## Infrastructure & Tools Used

| Component                | Version / Details     |
| ------------------------ | --------------------- |
| Cloud Provider           | AWS EC2               |
| Configuration Management | Ansible               |
| OS                       | Linux                 |
| JDK                      | OpenJDK 11            |
| Build Tool               | Maven                 |
| Database                 | MySQL Server          |
| Application Server       | Apache Tomcat 7.0.108 |
| Source Control           | Git                   |

## What the Playbook Does

The Ansible playbook automates the following steps:

1. **System preparation**

   * Installs OpenJDK 11
   * Installs Maven and Git
   * Verifies Java installation

2. **Database setup**

   * Installs MySQL Server
   * Starts and enables the MySQL service

3. **Application build**

   * Clones the `Spring3HibernateApp` repository from GitHub
   * Builds the application using Maven
   * Creates the WAR file

4. **Tomcat setup**

   * Downloads Apache Tomcat 7.0.108
   * Extracts Tomcat to `/opt/tomcat`
   * Creates the Tomcat systemd service
   * Starts and enables the Tomcat service

5. **Deployment**

   * Copies the generated WAR file to:
     `/opt/tomcat/apache-tomcat-7.0.108/webapps/`
   * Restarts the Tomcat service
   * Verifies that the application is running

# Task-wise Execution & Screenshots

Each task below corresponds to the required deployment steps. Add the actual screenshot below each placeholder.

## Connection Verification

Verify the connection between the Ansible control node and the AWS EC2 target server.

```bash
ansible all -m ping
```

![Screenshot 2026-08-27 at 11 34 57 PM](https://github.com/user-attachments/assets/3d1f7471-5fac-437b-ad43-5eb8735a6fa9)


**[Insert Connection Verification Screenshot Here]**

`![Connection Verification](screenshots/connection-verification.png)`

---

## Task 1: Install JDK 11

Install JDK 11 on the target EC2 instance.

```yaml
- name: Install JDK 11
  ansible.builtin.dnf:
    name: java-11-openjdk-devel
    state: present
```

Verify the Java installation:

```bash
java -version


**[Insert Install JDK 11 Screenshot

<img width="1440" height="900" alt="Screenshot 2026-08-27 at 11 44 37 PM" src="https://github.com/user-attachments/assets/fbb1ec7d-d930-49d2-a74b-2657b0644e2f" />


---

## Task 2: Install MySQL

Install MySQL Server and ensure that the MySQL service is running.

```yaml
- name: Install MySQL
  ansible.builtin.dnf:
    name: mysql-server
    state: present

- name: Start and enable MySQL
  ansible.builtin.service:
    name: mysqld
    state: started
    enabled: true
```

Verify MySQL:

```bash
systemctl status mysqld
```

### Screenshot

**[Insert Install MySQL Screenshot Here]**

`![Install MySQL](screenshots/task2-mysql.png)`

---

## Task 3: Create the WAR File for Spring3HibernateApp Using Maven

First clone the application repository:

```yaml
- name: Clone Spring3HibernateApp repository
  ansible.builtin.git:
    repo: https://github.com/opstree/spring3hibernate.git
    dest: /opt/spring3hibernate
    version: master
```

Install Maven and Git:

```yaml
- name: Install Maven and Git
  ansible.builtin.dnf:
    name:
      - maven
      - git
    state: present
```

Build the WAR file:

```yaml
- name: Build Spring3HibernateApp WAR
  ansible.builtin.shell:
    cmd: mvn clean package
    chdir: /opt/spring3hibernate
  environment:
    JAVA_HOME: /usr/lib/jvm/java-11-openjdk
```

The WAR file is generated inside:

```text
/opt/spring3hibernate/target/
```

Verify:

```bash
ls -lh /opt/spring3hibernate/target/
```

### Screenshot

**[Insert Create WAR File Screenshot Here]**

`![Build WAR with Maven](screenshots/task3-maven-war-build.png)`

---

## Task 4: Install Tomcat

Download and install Apache Tomcat 7.0.108.

Tomcat installation path:

```text
/opt/tomcat/apache-tomcat-7.0.108
```

Download URL:

```text
https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.108/bin/apache-tomcat-7.0.108.tar.gz
```

Create the Tomcat directory:

```yaml
- name: Create Tomcat directory
  ansible.builtin.file:
    path: /opt/tomcat
    state: directory
    mode: '0755'
```

Extract Tomcat:

```yaml
- name: Extract Tomcat
  ansible.builtin.unarchive:
    src: /tmp/apache-tomcat-7.0.108.tar.gz
    dest: /opt/tomcat
    remote_src: true
```

### Screenshot

**[Insert Install Tomcat Screenshot Here]**

`![Install Tomcat](screenshots/task4-install-tomcat.png)`

---

## Task 5: Send the WAR File to `/opt/tomcat/apache-tomcat-7.0.108/webapps/`

Copy the generated WAR file into the Tomcat `webapps` directory.

```yaml
- name: Copy WAR file to Tomcat webapps
  ansible.builtin.copy:
    src: /opt/spring3hibernate/target/Spring3HibernateApp.war
    dest: /opt/tomcat/apache-tomcat-7.0.108/webapps/Spring3HibernateApp.war
    remote_src: true
    mode: '0644'
```

Target location:

```text
/opt/tomcat/apache-tomcat-7.0.108/webapps/Spring3HibernateApp.war
```

### Screenshot

**[Insert WAR Deployment Screenshot Here]**

`![Copy WAR to webapps](screenshots/task5-deploy-war.png)`

---

## Task 6: Restart Tomcat Service

After deploying the WAR file, restart the Tomcat service.

```yaml
- name: Restart Tomcat service
  ansible.builtin.systemd:
    name: tomcat
    state: restarted
    daemon_reload: true
```

Verify Tomcat:

```bash
systemctl status tomcat
```

### Screenshot

**[Insert Restart Tomcat Screenshot Here]**

`![Restart Tomcat Service](screenshots/task6-restart-tomcat.png)`

---

# Final Output: Application Running

After successfully completing all tasks, verify that the Spring3HibernateApp is running.

If Tomcat is configured on port `8081`:

```text
http://<EC2-PUBLIC-IP>:8081/Spring3HibernateApp/
```

If Tomcat is running on the default port:

```text
http://<EC2-PUBLIC-IP>:8080/Spring3HibernateApp/
```

### Screenshot

**[Insert Final Application Running Screenshot Here]**

`![Application Running](screenshots/final-output-app-running.png)`

---

# Playbook Structure

```text
.
├── spring3hibernate.yml       # Main Ansible playbook
├── inventory                   # Ansible inventory file
├── screenshots/                # Task-wise screenshots
│   ├── connection-verification.png
│   ├── task1-jdk11.png
│   ├── task2-mysql.png
│   ├── task3-maven-war-build.png
│   ├── task4-install-tomcat.png
│   ├── task5-deploy-war.png
│   ├── task6-restart-tomcat.png
│   └── final-output-app-running.png
└── README.md
```

# Inventory Example

```ini
[app]
<EC2-PUBLIC-IP> ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/your-key.pem
```

# How to Run

1. Update the `inventory` file with your AWS EC2 instance details.

2. Verify Ansible connectivity:

```bash
ansible all -m ping
```

3. Run the playbook:

```bash
ansible-playbook -i inventory spring3hibernate.yml
```

4. Verify the Tomcat service:

```bash
systemctl status tomcat
```

5. Open the application in a browser:

```text
http://<EC2-PUBLIC-IP>:8081/Spring3HibernateApp/
```

# Verification

The deployment can be verified using the following commands.

### Verify Java

```bash
java -version
```

### Verify Maven

```bash
mvn -version
```

### Verify MySQL

```bash
systemctl status mysqld
```

### Verify Tomcat

```bash
systemctl status tomcat
```

### Verify WAR File

```bash
ls -lh /opt/tomcat/apache-tomcat-7.0.108/webapps/
```

### Verify Tomcat Port

```bash
ss -tulnp | grep 8081
```

### View Tomcat Logs

```bash
tail -f /opt/tomcat/apache-tomcat-7.0.108/logs/catalina.out
```

# Expected Result

After successful execution of the Ansible playbook:

* JDK 11 is installed.
* MySQL is installed and running.
* Maven and Git are installed.
* Spring3HibernateApp repository is cloned.
* WAR file is successfully created using Maven.
* Tomcat 7.0.108 is installed.
* WAR file is copied to the Tomcat `webapps` directory.
* Tomcat service is restarted.
* Spring3HibernateApp is successfully running.

# Conclusion

The complete Spring3HibernateApp infrastructure and deployment process is automated using Ansible on AWS EC2. This automation makes the deployment process faster, repeatable, and easier to manage.

## Author

**Jeetendra Singh**

