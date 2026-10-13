Here are **detailed, well-structured notes (exam-ready)** with **all important tables included** 👇

---

# ✅ 🔹 FTP (File Transfer Protocol)

## 📌 Introduction
- One of the **oldest Internet protocols**
- Works on **Application Layer** of TCP/IP
- Used for:
  - Uploading files to server  
  - Downloading files from server  
- Similar layer as web/email protocols

---

## 📡 Working of FTP

### 🔗 Two Connections

| Channel | Port | Purpose |
|--------|------|--------|
| Control Channel | 21 (TCP) | Send commands & receive status codes |
| Data Channel | 20 (TCP) | Transfer actual data |

---

### 🔄 Process
1. Client connects to server via **Port 21**
2. Sends commands (login, file operations)
3. Server replies with **status codes**
4. Data channel (Port 20) is created for file transfer
5. If interrupted → transfer can resume

---

## 🔄 FTP Modes

### ✔ Active FTP
- Client tells server which port to connect
- Server initiates data connection  
- ❌ Blocked by firewalls

### ✔ Passive FTP
- Server provides port  
- Client initiates connection  
- ✅ Works with firewalls

---

## 🔐 Authentication

- Requires:
  - Username  
  - Password  

### 🔓 Anonymous FTP
- Login using:
  ```
  username: anonymous
  password: (any email)
  ```
- Limited access  
- Used for public file sharing  
- ⚠️ Security risk

---

## 📜 FTP Commands & Status Codes

### 🔹 Common Commands

| Command | Description |
|--------|------------|
| USER | Enter username |
| PASS | Enter password |
| LIST (ls) | List files |
| GET | Download file |
| PUT | Upload file |
| DELETE | Delete file |
| QUIT | Exit |

---

### 🔹 Status Codes

| Code | Meaning |
|-----|--------|
| 220 | Service ready |
| 230 | Login successful |
| 150 | File transfer starting |
| 226 | Transfer complete |

---

## ⚠️ Security Issues

- **Plain text protocol** (no encryption)
- Credentials can be **sniffed**
- Vulnerable to:
  - Brute force attacks  
  - Unauthorized access  

---

# ✅ 🔹 TFTP (Trivial File Transfer Protocol)

## 📌 Introduction
- Simpler version of FTP  
- Uses **UDP (not reliable)**  

---

## ❌ Limitations

- No authentication  
- No encryption  
- No directory listing  

---

## 📂 Access Control
- Based only on:
  - File permissions  
- Works in:
  - Shared/public directories  

---

## 📜 TFTP Commands

| Command | Description |
|--------|------------|
| connect | Set remote host |
| get | Download file |
| put | Upload file |
| quit | Exit |
| status | Show transfer info |
| verbose | Show detailed output |

---

## ⚠️ Security
- Very insecure  
- Used only in:
  - Local networks  
  - Trusted environments  

---

# ✅ 🔹 Default FTP Server: vsFTPd

## 📌 Introduction
- Popular FTP server in Linux  
- Config file:
```
/etc/vsftpd.conf
```

---

## ⚙️ Default Configuration

| Setting | Description |
|--------|------------|
| listen=NO | Run via inetd |
| listen_ipv6=YES | Enable IPv6 |
| anonymous_enable=NO | Disable anonymous login |
| local_enable=YES | Allow local users |
| dirmessage_enable=YES | Show directory messages |
| use_localtime=YES | Use local time |
| xferlog_enable=YES | Enable logging |
| connect_from_port_20=YES | Use port 20 |
| secure_chroot_dir | Empty secure directory |
| pam_service_name=vsftpd | PAM authentication |
| rsa_cert_file | SSL certificate |
| rsa_private_key_file | SSL key |
| ssl_enable=NO | Disable SSL |

---

## 📂 FTP Users Restriction

File:
```
/etc/ftpusers
```

### Example

| Users blocked |
|--------------|
| guest |
| john |
| kevin |

👉 These users **cannot login**

---

# ⚠️ 🔹 Dangerous Settings

| Setting | Description |
|--------|------------|
| anonymous_enable=YES | Allow anonymous login |
| anon_upload_enable=YES | Allow file upload |
| anon_mkdir_write_enable=YES | Allow directory creation |
| no_anon_password=YES | No password required |
| anon_root=/home/user/ftp | Anonymous directory |
| write_enable=YES | Allow file modifications |

---

# 🔍 🔹 FTP Enumeration (Important)

### Steps
1. Connect:
```
ftp <IP>
```

2. Try login:
```
anonymous
```

3. Use commands:
- `ls` → list files  
- `status` → show connection info  
- `debug`, `trace` → detailed logs  

---

## 📊 Example Output (Important)

```
220 Welcome to FTP
230 Login successful
150 Opening data connection
226 Transfer complete
```

---

# 🛡️ 🔹 Security Features

| Setting | Purpose |
|--------|--------|
| dirmessage_enable=YES | Show directory messages |
| chown_uploads=YES | Change file ownership |
| chown_username=user | Set file owner |
| local_enable=YES | Allow local users |
| chroot_local_user=YES | Restrict user directory |
| chroot_list_enable=YES | Use restricted user list |

---

## 🔐 Additional Security Settings

| Setting | Description |
|--------|------------|
| hide_ids=YES | Hide user/group IDs |
| ls_recurse_enable=YES | Enable recursive listing |

---

# 📂 Recursive Listing Example

```
ls -R
```

👉 Shows:
- All directories  
- All subdirectories  
- All files  

---

# 💡 🔹 Attack Possibilities (Very Important)

- **Sniffing** → steal credentials  
- **Anonymous misuse**  
- **Brute force attacks**  
- **File upload → exploit (RCE)**  
- **Log injection attacks**  

---

# 🔄 🔹 FTP vs TFTP

| Feature | FTP | TFTP |
|--------|-----|------|
| Protocol | TCP | UDP |
| Reliability | High | Low |
| Authentication | Yes | No |
| Security | Low | Very Low |
| Directory listing | Yes | No |
| Usage | Internet | Local network |

---

## Download via FTP

```
get Important\ Notes.txt
```
- To download  all files and folders:
``` shellsession
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```
- wget create a directory with the name of the IP address and store all downloaded files.
```
tree .
```
- list structure .
---
## UPLOAD file
```
put file.txt
```
---

# ✅ 🔹 Footprinting FTP Service (Using Nmap)

## 📌 What is Footprinting?

- Process of **gathering information** about a target system
    
- Goal:
    
    - Identify services
        
    - Find vulnerabilities
        

---

# 🔍 🔹 Why Use Nmap?

- Detects:
    
    - Open ports
        
    - Running services
        
    - Service versions
        
- Uses **NSE (Nmap Scripting Engine)** for deeper analysis
    

---

# ⚙️ 🔹 Updating NSE Scripts

```bash
sudo nmap --script-updatedb
```

👉 Updates script database  
👉 Ensures latest vulnerability checks

---

# 📂 🔹 Location of NSE Scripts

```bash
/usr/share/nmap/scripts/
```

---

## 📜 Common FTP NSE Scripts

|Script|Purpose|
|---|---|
|ftp-anon.nse|Check anonymous login|
|ftp-brute.nse|Brute-force login|
|ftp-syst.nse|Get server info|
|ftp-vsftpd-backdoor.nse|Detect backdoor|
|ftp-bounce.nse|Check bounce attack|
|ftp-libopie.nse|Check auth issues|

---

# 🔎 🔹 Nmap Scan Command

```bash
sudo nmap -sV -p21 -sC -A 10.129.14.136
```

### ✔ Options Explained

|Option|Meaning|
|---|---|
|`-sV`|Version detection|
|`-p21`|Scan FTP port|
|`-sC`|Default scripts|
|`-A`|Aggressive scan|

---

# 📊 🔹 Scan Output Analysis

## ✔ Basic Info

|Field|Meaning|
|---|---|
|`21/tcp open`|FTP port open|
|`ftp`|Service name|
|`vsftpd 2.0.8+`|FTP server version|

---

## 🔓 Anonymous Login

```
ftp-anon: Anonymous FTP login allowed (230)
```

👉 Means:

- No password needed
    
- Anyone can access
    

---

## 📂 Directory Listing

|File/Folder|Permission|Risk|
|---|---|---|
|Calendar.pptx|rwx|Writable|
|Clients/|rwx|Writable|
|Documents/|rwx|Writable|
|Employees/|rwx|Writable|
|testupload.txt|rwx|Writable|

👉 **All are writable → VERY dangerous ⚠️**

---

# 🔐 🔹 FTP Server Info (ftp-syst)

Shows:

- Logged-in user
    
- Connection type
    
- Server version
    
- Security status
    

---

## 📊 Example Info

|Field|Meaning|
|---|---|
|Logged in as ftp|Anonymous user|
|TYPE: ASCII|Transfer mode|
|Timeout: 300|Session timeout|
|Plain text|❌ No encryption|
|vsFTPd 3.0.3|Server version|

---

# ⚠️ 🔹 Major Security Findings

|Issue|Impact|
|---|---|
|Anonymous login|Unauthorized access|
|Writable files|Upload malware|
|Plain text|Credentials sniffing|
|FTP connected to web|RCE possible|

---

# 🔬 🔹 Script Trace Option

```bash
--script-trace
```

👉 Shows:

- Commands sent
    
- Ports used
    
- Server responses
    

---

## 📡 What happens internally?

|Step|Action|
|---|---|
|CONNECT|Client connects|
|Multiple ports|Parallel scans|
|Banner received|Server info shown|

---

# 🔌 🔹 Manual Interaction

## ✔ Using netcat

```bash
nc -nv <IP> 21
```

## ✔ Using telnet

```bash
telnet <IP> 21
```

👉 Used for:

- Banner grabbing
    
- Manual commands
    

---

# 🔐 🔹 FTP over SSL/TLS

## ✔ Command

```bash
openssl s_client -connect <IP>:21 -starttls ftp
```

---

## 📜 What we get

|Info|Use|
|---|---|
|Certificate|Identify organization|
|CN (hostname)|Server name|
|Email|Admin contact|
|Location|Company details|

---

# 🧠 🔹 Why Certificate is Important

- Reveals:
    
    - Internal domain names
        
    - Organization info
        
    - Email IDs
        
- Helps in:
    
    - Reconnaissance
        
    - Social engineering
        

---

# 🚨 🔹 Attack Possibilities

|Vulnerability|Result|
|---|---|
|Anonymous access|Data exposure|
|Writable directories|Upload shell|
|FTP + Web server|Remote code execution|
|Plain text|Credential theft|

---
