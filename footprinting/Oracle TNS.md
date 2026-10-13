This comprehensive overview covers the **Oracle Transparent Network Substrate (TNS)**, its configuration, security mechanisms, and the tools used to audit it.

---

## 🏛️ Oracle TNS: The Communication Backbone

**Transparent Network Substrate (TNS)** is the proprietary Oracle foundation that allows different types of software and databases to talk to each other over a network, regardless of the underlying hardware or protocol (TCP/IP, IPv6, etc.).

### Core Functions
* **Name Resolution:** Translates user-friendly service names (like `ORCL`) into network addresses.
* **Connection Management:** Establishes, maintains, and terminates paths between clients and the database.
* **Load Balancing:** Distributes incoming traffic across multiple database instances to prevent bottlenecks.
* **Security:** Provides a layer above TCP/IP to support SSL/TLS encryption and authorized host filtering.



---

## ⚙️ Default Configuration & Connectivity

Oracle's behavior changes significantly depending on the version you encounter.

### 1. Ports and Protocols
* **Default Port:** **TCP/1521**.
* **Management:** Remote management was enabled by default in **Oracle 8i/9i** but disabled in **10g/11g** for security.
* **Protocols Supported:** TCP/IP, UDP, IPX/SPX, and AppleTalk.

### 2. The Configuration Files (The "Big Two")
These files are usually found in `$ORACLE_HOME/network/admin`.

* **`tnsnames.ora` (Client-Side):** This is the "address book." It tells the client *where* to go.
    * *Example Entry:* `ORCL = (DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))...)`
* **`listener.ora` (Server-Side):** This is the "gatekeeper." It tells the server *what* to listen for and which database (SID) to forward requests to.

### 3. Default Credentials to Remember
| Service | Default Password | Notes |
| :--- | :--- | :--- |
| **Oracle 9** | `CHANGE_ON_INSTALL` | Often left unchanged by lazy admins. |
| **Oracle 10/11** | (No default) | Admin must set one during install. |
| **DBSNMP** | `dbsnmp` | Used for monitoring; frequently overlooked. |

---

## 🛡️ Security Mechanisms

### The PL/SQL Exclusion List (`PlsqlExclusionList`)
This is a **Blacklist** feature. By placing a text file in the `$ORACLE_HOME/sqldeveloper` directory, admins can block specific PL/SQL packages from being executed through the web-based Oracle Application Server. This prevents attackers from calling dangerous internal functions.

### Network Access Control
The listener can be configured to perform basic authentication by filtering:
* Authorized Hostnames
* Allowed IP Addresses
* Standard User/Password combos

---

## 🛠️ Penetration Testing: ODAT (Oracle Database Attacking Tool)

**ODAT** is the "Swiss Army Knife" for attacking Oracle. It is a Python-based modular tool used to discover and exploit misconfigurations.

### Key ODAT Modules
* **`sidguesser`:** Finds the System ID (SID) needed to connect.
* **`passwordguesser`:** Performs brute-force attacks against database users.
* **`tnscmd` / `tnspoison`:** Tests for vulnerabilities in the TNS protocol itself.
* **`utlfile` / `externaltable`:** Attempts to read or write files on the server's OS.



---

## 📝 Configuration Cheat Sheet

| Parameter | Purpose |
| :--- | :--- |
| **SERVICE_NAME** | The "Alias" the client uses to find the DB. |
| **SID_NAME** | The actual ID of the database instance on the server. |
| **CONNECT_DATA** | Details like SID or Service Name required for the handshake. |
| **PROTOCOL** | Usually TCP, but can be IPC (Inter-Process Communication). |
| **SSL_VERSION** | Dictates which version of encryption (TLS 1.2, etc.) to use. |

---

### ⚠️ Security Warning: The "Finger" Service
Some legacy environments still run the **Finger** service alongside Oracle. If an attacker can use Finger to find a user's home directory path, they can often guess the location of Oracle configuration files (`$ORACLE_HOME`), making the entire database much easier to compromise.

---
These notes cover the practical interaction with an Oracle database using **SQL*Plus** and the **ODAT** tool, focusing on authentication, enumeration, privilege escalation, and file system interaction.

---

## 🛠️ Interacting with Oracle: SQL*Plus

**SQL*Plus** is the command-line tool used to connect to and manage Oracle databases.

### 1. Connecting to the Database

The standard connection string follows this format:

`sqlplus username/password@IP/SID`

- **Example:** `sqlplus scott/tiger@10.129.204.235/XE`
    
- **Common Error:** If you see `libsqlplus.so: cannot open shared object file`, it means your system doesn't know where the Oracle libraries are. Fix this by adding the path to your library configuration (`ldconfig`).
    

---

## 🔍 Database Enumeration

Once connected, you use SQL queries to map out the environment.

### 1. Listing Tables

To see what data is available in the current database:

SQL

```
SELECT table_name FROM all_tables;
```

### 2. Checking Privileges

To see what your current user is allowed to do:

SQL

```
SELECT * FROM user_role_privs;
```

- **Note:** A standard user like `scott` usually has basic roles like `CONNECT` and `RESOURCE`.
    

---

## 🚀 Privilege Escalation: The "sysdba" Trick

In Oracle, `sysdba` is a special administrative privilege that gives you full control over the database instance.

- **The Command:** `sqlplus scott/tiger@IP/SID as sysdba`
    
- **Why it works:** If the database is misconfigured or if the user has been granted high-level administrative rights, you can log in as the **SYS** user even while using your low-level credentials. This effectively makes you the "Root" of the database.
    

---

## 🔓 Post-Exploitation: Data Exfiltration

Once you have `sysdba` access, you can access internal system tables that are normally hidden.

### 1. Extracting Password Hashes

Oracle stores user password hashes in the `sys.user$` table. You can extract these to crack them offline (using tools like Hashcat or John the Ripper).

SQL

```
SELECT name, password FROM sys.user$;
```

---

## 📂 Advanced Exploitation: File Upload

If the database server is also running a web server (like Apache on Linux or IIS on Windows), you can attempt to upload a **Web Shell** to gain Operating System (OS) access.

### 1. Target Paths

You must guess or find the "Web Root" directory:

- **Linux:** `/var/www/html`
    
- **Windows:** `C:\inetpub\wwwroot`
    

### 2. Using ODAT for File Upload

The **ODAT** tool has a module called `utlfile` specifically for writing files to the server's disk.

- **The Command:** `./odat.py utlfile -s <IP> -d <SID> -U <User> -P <Pass> --sysdba --putFile <RemotePath> <LocalFile>`
    

### 3. Verification

After uploading, always verify the file is reachable via a web browser or `curl`:

Bash

```
curl -X GET http://10.129.204.235/testing.txt
```

---
### Enumerate the target Oracle database and submit the password hash of the user DBSNMP as the answer.

```
sqlplus scott/tiger@10.129.205.19/XE as sysdba
select name, password from sys.user$;
```
- E066D214D5421CCC