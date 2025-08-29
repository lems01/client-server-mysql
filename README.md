# Client-Server Architecture with MySQL on AWS EC2

## 📌 Project Overview
This project demonstrates how to implement a **client-server architecture** using the **MySQL Database Management System (DBMS)** deployed on **AWS EC2 instances**.  
- The **server node** hosts the MySQL database.  
- The **client node** connects remotely to the database to perform queries.  

This setup mimics real-world distributed systems where applications and databases are hosted on separate machines.

---

## 🛠️ Architecture
- **Server EC2 Instance** → MySQL DBMS installed.  
- **Client EC2 Instance** → MySQL client installed.  
- **AWS Security Groups** control access.  

```
              +-------------------+
              |   AWS VPC/Subnet  |
              |                   |
   +----------+----------+   +----+-----------+
   |  Client EC2         |   |  Server EC2    |
   |  (MySQL Client)     |   |  (MySQL Server)|
   |                     |   |                |
   | mysql -h <serverIP> |   | Port 3306 open |
   +----------+----------+   +--------+-------+
              |                       |
              +--------- Port 3306 ----+
```

---

## 🚀 Prerequisites
- An active AWS account.  
- At least **two EC2 instances** (Ubuntu/Debian/RedHat).  
- Security group rules configured:  
  - Port **22** → SSH access.  
  - Port **3306** → MySQL database access.  

---

## ⚙️ Step 1: Launch EC2 Instances
1. Log into your AWS console.  
2. Launch **two EC2 instances**:  
   - One as **MySQL Server**.  
   - One as **MySQL Client**.  
3. Attach both to the same **VPC/subnet** for ease of communication.  
4. Configure security groups to allow inbound traffic on port **3306** from the client’s IP.

---

## ⚙️ Step 2: Install MySQL on Server
SSH into the **Server instance**:

```bash
sudo apt update -y
sudo apt install mysql-server -y
```

Start and enable MySQL service:

```bash
sudo systemctl start mysql
sudo systemctl enable mysql
```

---

## ⚙️ Step 3: Configure MySQL for Remote Access
1. Log in to MySQL:

```bash
sudo mysql -u root -p
```

2. Create a new user and grant access:

```sql
CREATE USER 'lemssel'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'dbuser'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

3. Edit MySQL config file to allow remote connections:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Change `bind-address` from `127.0.0.1` to `0.0.0.0`.

4. Restart MySQL:

```bash
sudo systemctl restart mysql
```

---

## ⚙️ Step 4: Install MySQL Client on Client Instance
SSH into the **Client instance**:

```bash
sudo apt update -y
sudo apt install mysql-client -y
```

---

## ⚙️ Step 5: Connect Client to Server
From the **Client instance**, run:

```bash
mysql -h <SERVER_PRIVATE_IP> -u dbuser -p
```

If using public IPs, ensure the **Server’s security group** allows inbound connections from the Client’s public IP on port **3306**.

---

## 📊 Testing
1. Create a database on the server:

```sql
CREATE DATABASE testdb;
USE testdb;
CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100));
INSERT INTO users (name) VALUES ('Alice'), ('Bob');
```

2. From the client:

```sql
USE testdb;
SELECT * FROM users;
```

Expected result:

```
+----+-------+
| id | name  |
+----+-------+
|  1 | Alice |
|  2 | Bob   |
+----+-------+
```


## 📌 Conclusion
You have successfully implemented a simple **client-server architecture using MySQL DBMS on AWS EC2**.