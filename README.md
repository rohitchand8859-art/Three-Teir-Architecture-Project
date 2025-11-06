# 3-TIER AECHITECTURE PROJECT IN MYSQL FOR STUDENT REGISTRATION FORM 


This project demonstrates a ** 3-Tier Architecture** setup on **AWS**, including:
- **Frontend (HTML/CSS)**
- **Backend (PHP)**
- **Database (MySQL)**

## 🧩 Architecture Overview

**Layers:**
1. **Frontend Layer (Web-server):**
   - Contains HTML and CSS files.
   - Deployed on a Public Subnet EC2 with Nginx.
    

2. **Backend Layer (App-server):**
   - Contains PHP logic to process requests.
   - Deployed on a Private Subnet EC2 with PHP and Nginx.

3. **Database Layer (DB-server):**
   - Contains MySQL database for storing data.
   - Deployed on a Private Subnet EC2 with MySQL.

---

## ⚙️ Steps to Set Up

### 1️⃣ Create Network (VPC and Subnets)

1. Create a VPC (CIDR: 10.0.0.0/24) with:
   - Internet Gateway
   - Custom Route Table
   - One public subnet (Web-Server)
   - Two private subnets (App-Server, DB-Server)
2. Create 3 Subnets:
   - Public Subnet → Frontend
   - Private Subnet 1 → Backend
   - Private Subnet 2 → Database
3. Attach Internet Gateway to VPC.
4. Create Route Tables:
   - Public RT → 0.0.0.0/0 → IGW
   - Private RT → internal communication only.
5. *EC2 Instances*:
   - Web Server (Public Subnet)
   - App Server (Private Subnet)
   - DB Server (Private Subnet)
   - create separate EC2 instances in separate subnets.


---

### 2️⃣ Launch EC2 Instances

| Tier | Subnet Type | Purpose | Software |
|------|--------------|----------|-----------|
| Frontend | Public | Host HTML/CSS | Nginx |
| Backend | Private | Handle PHP logic | Nginx + PHP |
| Database | Private | Store data | MySQL |

---

### 3️⃣ Frontend Setup

1. SSH into frontend instance:
   ```bash
   sudo yum install nginx -y
   sudo systemctl enable nginx.service
   sudo systemctl start nginx.service
   ```
2. Editing Nginx HTML
   ~~~bash
   sudo nano /usr/share/nginx/html/form.html
   sudo nano /etc/nginx/nginx.conf
   ## In location block, set App server's private IP :

3. Reload Nginx
    ~~~bash
    sudo service nginx reload
    sudo service nginx restart

   ```
4. Edit **form.html** → replace `<BACKEND-PRIVATE-IP>` with backend instance private IP.

---

### 4️⃣ Backend Setup

1. SSH into backend instance:
   ```bash
   sudo yum install nginx php  mariadb105-server php-mysqlnd -y
   sudo systemctl enable nginx.service
   sudo systemctl enable php-fpm.service
   sudo systemctl start nginx.service
   sudo systemctl start php-fpm.service

  2. Place PHP files:

    cd /usr/share/nginx/html/
    sudo nano submit.php

   
   
  3. Upload `submit.php` into `/usr/share/nginx/html/`.
  4. Edit **submit.php** → replace `<DB-SERVER-PRIVATE-IP>` with database instance private IP.

---

### 5️⃣ Database Setup

1. SSH into DB instance:
   ```bash
   sudo yum install mariadb105-server -y
   sudo systemctl enable mariadb.service
   sudo systemctl start mariadb.service
   ```
2. Log in to MySQL:
   ```bash
   mysql -u root -p
3. Setup DB & user (example):
 ```bash

 MariaDB [(none)]> alter user 'root'@'localhost' identified by 'Pass@123';
 MariaDB [(none)]> create user 'rohit'@'192.168.7.203' identified by "Rohit@123";
 MariaDB [(none)]> create database studentdb;
 MariaDB [(none)]> use studentdb;
 MariaDB [studentdb]> create table students
 MariaDB [studentdb]> Grant all privileges on studentdb.* to 'rohit'@'192.168.7.203';
 MariaDB [studentdb]> flush privileges;
 

   ```
3. Run SQL script:
   ```bash
   SOURCE /path/to/studentdb.sql;
   ```

---

## 🌐 How It Works

- User visits **Frontend** via public IP.
- Frontend form sends data to **Backend** (`submit.php`).
- Backend connects to **MySQL** on the **Database** instance.
- Data gets stored in the database table `students`.

---

## 📁 Folder Structure

```
3-Tier-Architecture-project/
│
├── Frontend/
│   ├── form.html
│   └── style.css
│
├── Backend/
│   └── submit.php
│
├── Database/
│   └── studentdb; mysql
│
└── README.md
```

---

## 💡 Notes
  The Web Server is configured to function as a bastion (jump) host, enabling secure administrative access to resources within the private subnets.

  Use SCP to securely transfer the private .pem key to the Web Server. This allows you to SSH into the Application and Database servers, which are not directly     accessible from the internet.


 - Security Groups:
  - Frontend → allow HTTP (80) + SSH (22)
  - Backend → allow HTTP (80) from Frontend SG only
  - Database → allow MySQL (3306) from Backend SG only
- Replace placeholders `<BACKEND-SERVER-PRIVATE-IP>` and `<DB-SERVER-PRIVATE-IP>` in your files.

---


## 💡 AUTHOR 
**ROHIT CHAND**
