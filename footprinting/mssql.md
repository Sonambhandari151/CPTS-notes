

## **1. What is MSSQL**

- MSSQL = Microsoft’s **Relational Database Management System (RDBMS)**
    
- Developed by **Microsoft**
    
- Uses **SQL (Structured Query Language)**
    
- Closed-source (unlike MySQL)
    
- Mainly runs on **Windows**, but also available on Linux/Mac
    

---

## **2. Key Features**

- Strong integration with **.NET framework**
    
- High performance & scalability
    
- Enterprise-level security & management
    
- Supports **Transact-SQL (T-SQL)** (extended SQL)
    

---

## **3. MSSQL Clients**

Used to connect and manage databases:

- **SQL Server Management Studio (SSMS)** → GUI tool
    
- Command-line / tools:
    
    - `mssql-cli`
        
    - PowerShell
        
    - HeidiSQL
        
    - SQLPro
        
    - **Impacket mssqlclient.py** (very useful in pentesting)
        

👉 Important:

- Clients can be installed **anywhere**, not just on server
    
- May contain **saved credentials → security risk**
    

---

## **4. Default System Databases**

|Database|Purpose|
|---|---|
|`master`|Stores system-level info (users, configs)|
|`model`|Template for new databases|
|`msdb`|Handles jobs, alerts (SQL Agent)|
|`tempdb`|Temporary data storage|
|`resource`|Read-only system objects|

---

## **5. Default Configuration**

- Default port: **1433**
    
- Service runs as:
    
    ```
    NT SERVICE\MSSQLSERVER
    ```
    
- Authentication types:
    
    - **Windows Authentication** (uses OS / Active Directory)
        
    - SQL Authentication (username/password)
        

👉 By default:

- Encryption **not enforced**
    
- Can allow insecure connections
    

---

## **6. Authentication**

### **Windows Authentication**

- Uses:
    
    - Local SAM OR
        
    - Active Directory (Domain Controller)
        
- More secure but:
    
    - If account compromised → **full domain risk**
        

---

## **7. Dangerous / Misconfigured Settings**

- No encryption used → data in plain text
    
- Self-signed certificates → can be spoofed
    
- Named pipes enabled → lateral movement risk
    
- Weak/default credentials:
    
    - `sa` account often weak or enabled
        
- Saved credentials in clients (SSMS)
    

---

## **8. Footprinting MSSQL**

### **Nmap Scan**

```bash
nmap -sV -p1433 --script ms-sql-info <IP>
```

### Advanced scan:

```bash
nmap -p1433 --script ms-sql-* <IP>
```

---

## **9. Information from Nmap**

- Server name
    
- Instance name (e.g., MSSQLSERVER)
    
- Version (e.g., SQL Server 2019)
    
- Port (1433)
    
- Named pipes
    
- OS info
    

---

## **10. Metasploit Enumeration**

Module:

```bash
auxiliary/scanner/mssql/mssql_ping
```

Provides:

- Server name
    
- Instance name
    
- Version
    
- Port
    
- Named pipe path
    

---

## **11. Connecting to MSSQL (Impacket)**

```bash
python3 mssqlclient.py user@IP -windows-auth
```

After connection:

```sql
select name from sys.databases;
```

---

## **12. Basic MSSQL Commands**

- List databases:
    

```sql
select name from sys.databases;
```

- Use database:
    

```sql
use <db_name>;
```

- Show tables:
    

```sql
select * from information_schema.tables;
```

---

## **13. Important Concepts**

### Instance

- MSSQL can have multiple **instances**
    
- Default: `MSSQLSERVER`
    

### Named Pipes

- Communication method:
    

```
\\<host>\pipe\sql\query
```

---

## **14. Security Risks**

- Weak `sa` password → full DB access
    
- No encryption → sniffing possible
    
- Misconfigured access → external exposure
    
- Credential reuse → lateral movement
    
- SQL Injection → DB compromise
    

---

## **15. Pentesting Key Points**

- Scan port **1433**
    
- Try:
    
    - Default creds
        
    - Weak passwords
        
- Use:
    
    - Nmap scripts
        
    - Metasploit
        
    - `mssqlclient.py`
        
- Enumerate:
    
    - Databases
        
    - Users
        
    - Permissions
        

---
### Enumerate the target using the concepts taught in this section. List the hostname of MSSQL server.
```
nmap -p1433 --script ms-sql-info,ms-sql-ntlm-info 10.129.53.44
```
- ILF-SQL-01

### Connect to the MSSQL instance running on the target using the account (backdoor:Password1), then list the non-default database present on the server.
```
impacket-mssqlclient backdoor@10.129.53.44 -windows-auth
select name from sys.databases;
```
- Employees