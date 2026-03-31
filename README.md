
---

# 🚀 Student Registration App Deployment Guide

This project is a **Java Web Application (JSP + Servlet + DAO + MySQL)** deployed using:

* Jenkins (CI/CD)
* Apache Tomcat (Application Server)
* MySQL (Database)
* AWS EC2 (Hosting)

---

# 📌 Architecture

```
Jenkins → Build (Maven) → WAR
        ↓
Deploy to Tomcat
        ↓
JSP → Servlet → DAO → JNDI → MySQL
```

---

# ⚙️ Prerequisites

* AWS EC2 instance (Amazon Linux)
* Java 11 or 17
* Jenkins installed
* Apache Tomcat installed
* MySQL (MariaDB) installed

---

# 🔧 Step 1: Install Java

```bash
sudo dnf install -y java-17-openjdk
java -version
```

---

# 🐱 Step 2: Install Tomcat
add user
```bash
sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
```

```bash
cd /tmp
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.116/bin/apache-tomcat-9.0.116.tar.gz

sudo mkdir /opt/tomcat
sudo tar -xvzf apache-tomcat-9.0.116.tar.gz -C /opt/tomcat --strip-components=1
```

Set permissions:

```bash
sudo chown -R tomcat:tomcat /opt/tomcat
```

Start Tomcat:

```bash
sudo systemctl start tomcat
```

---

# 🔌 Step 3: Change Tomcat Port (Optional)

Edit:

```bash
sudo vi /opt/tomcat/conf/server.xml
```

Change:

```xml
port="8080" → port="9090"
```

Restart:

```bash
sudo systemctl restart tomcat
```

---

# 🛢️ Step 4: Install MySQL (MariaDB)

```bash
sudo dnf install -y mariadb105-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

Secure installation:

```bash
sudo mysql_secure_installation
```

---

# 🗄️ Step 5: Create Database

```sql
CREATE DATABASE test;
USE test;

CREATE TABLE students (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    student_name VARCHAR(100),
    student_addr VARCHAR(100),
    student_age VARCHAR(10),
    student_qual VARCHAR(50),
    student_percent VARCHAR(10),
    student_year_passed VARCHAR(10)
);
```

---

# 👤 Step 6: Create DB User

```sql
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON test.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;
```

---

# 🔗 Step 7: Configure JNDI (Tomcat)

Edit:

```bash
sudo vi /opt/tomcat/conf/context.xml
```

Add inside `<Context>`:

```xml
<Resource name="jdbc/TestDB"
          auth="Container"
          type="javax.sql.DataSource"
          maxTotal="20"
          maxIdle="10"
          maxWaitMillis="-1"
          username="appuser"
          password="password"
          driverClassName="com.mysql.cj.jdbc.Driver"
          url="jdbc:mysql://localhost:3306/test?useSSL=false"/>
```

---

# 📦 Step 8: Add MySQL Driver

```bash
cd /opt/tomcat/lib
wget https://repo1.maven.org/maven2/com/mysql/mysql-connector-j/8.0.33/mysql-connector-j-8.0.33.jar
```

Restart Tomcat:

```bash
sudo systemctl restart tomcat
```

---

# 🧾 Step 9: Configure web.xml

Add resource reference:

```xml
<resource-ref>
    <res-ref-name>jdbc/TestDB</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
```

---

# 🏗️ Step 10: Fix Maven Plugin

Update `pom.xml`:

```xml
<plugin>
    <artifactId>maven-war-plugin</artifactId>
    <version>3.3.2</version>
</plugin>
```

---

# 🔄 Step 11: Jenkins Pipeline

```groovy
pipeline {
    agent any
    
    tools {
        maven 'm3'
    }

    stages {
        stage('fetch') {
            steps {
                git 'https://github.com/NetTech-Trainer/student-ui.git'
            }
        }

        stage('build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('deploy') {
            steps {
                sh 'cp target/studentapp-2.2-SNAPSHOT.war /opt/tomcat/webapps/studentapp.war'
            }
        }
    }
}
```

---

# 🌐 Step 12: Access Application

```
http://<EC2-IP>:9090/studentapp
```

---

# 🔍 Troubleshooting

## 404 Error

* Check WAR file in `/webapps`
* Verify correct URL
* Check Tomcat logs

## DB Connection Issue

* Verify MySQL is running
* Check JNDI config
* Ensure driver is in `/opt/tomcat/lib`

## View Logs

```bash
sudo tail -f /opt/tomcat/logs/catalina.out
```

---

# 🎯 Final Outcome

✔ Application deployed on Tomcat
✔ Connected to MySQL via JNDI
✔ CI/CD working with Jenkins
✔ Accessible via browser

---

# 💡 Notes

* WAR file name determines URL
* Always restart Tomcat after config changes
* Use logs to debug errors (`SEVERE`, `ERROR`)

---

# 🚀 Author

DevOps Learning Project – Student Registration System

---

