

## **1. What is MySQL**

- Open-source **Relational Database Management System (RDBMS)**
    
- Developed by **Oracle Corporation**
    
- Stores data in **tables (rows + columns)**
    
- Uses **SQL (Structured Query Language)**
    

---

## **2. Working**

- Follows **Client–Server model**
    
    - **Server** → stores & manages data
        
    - **Client** → sends queries (read/write)
        

---

## **3. Uses**

- Dynamic websites (e.g., **WordPress**)
    
- Stores:
    
    - Users, passwords
        
    - Emails, data
        
    - Website content
        

---

## **4. LAMP / LEMP Stack**

- LAMP = Linux + Apache + MySQL + PHP
    
- LEMP = Linux + Nginx + MySQL + PHP
    

---

## **5. MariaDB**

- Fork of MySQL
    
- Created after MySQL was acquired by Oracle
    

---

## **6. Important MySQL Commands**

- Connect:
    
    ```bash
    mysql -u user -pPASSWORD -h IP
    ```
    
- Show DBs:
    
    ```sql
    show databases;
    ```
    
- Select DB:
    
    ```sql
    use db_name;
    ```
    
- Show tables:
    
    ```sql
    show tables;
    ```
    
- View data:
    
    ```sql
    select * from table;
    ```
    

---

## **7. Default Configuration**

- Port: **3306**
    
- Important paths:
    
    - Data: `/var/lib/mysql`
        
    - Config: `/etc/mysql/`
        

---

## **8. Important Databases**

- `mysql` → user accounts & permissions
    
- `information_schema` → metadata
    
- `sys` → system info
    

---

## **9. Dangerous Settings**

- `user` → service user
    
- `password` → stored in config (risk)
    
- `admin_address` → network exposure
    
- `secure_file_priv` → file read/write control
    
- `debug`, `sql_warnings` → leak sensitive info
    

---

## **10. Security Issues**

- Weak/empty passwords
    
- Open access on port **3306**
    
- Plain-text credentials in config
    
- SQL Injection attacks
    

---

## **11. Enumeration (Nmap)**

```bash
nmap -sV -sC -p3306 --script mysql* <IP>
```

Finds:

- Users
    
- Password issues
    
- Version info
    

---

## **12. Common Attack Findings**

- Empty passwords
    
- Multiple valid users
    
- Info leakage from errors
    

---
### Enumerate the MySQL server and determine the version in use. (Format: MySQL X.X.XX)
```
nmap -sV -sC -p 3306 --script mysql-info 10.129.53.29
```
- MySQL 8.0.27

### During our penetration test, we found weak credentials "robin:robin". We should try these against the MySQL server. What is the email address of the customer "Otto Lang"?
```
 mysql -u robin -probin -h 10.129.53.29 --skip-ssl

```
- ultrices@google.htb