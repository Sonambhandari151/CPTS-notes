
## 1. IMAP (Internet Message Access Protocol)

- Used to **access and manage emails on server**
    
- Works on **client-server model**
    
- Provides **synchronization across multiple devices**
    
- Emails remain on server until deleted
    

### Features

- Folder structure (hierarchical mailboxes)
    
- Access multiple mailboxes in one session
    
- Search & manage emails directly on server
    
- Multi-client synchronization
    
- Supports offline mode (sync later)
    

### Working

- Port:
    
    - **143 → normal**
        
    - **993 → SSL/TLS (secure)**
        
- Uses **text-based ASCII commands**
    
- Allows **pipelining** (multiple commands without waiting)
    

### Example Commands

- `LOGIN username password`
    
- `LIST "" *` → list folders
    
- `SELECT INBOX`
    
- `FETCH <id> all`
    
- `DELETE mailbox`
    
- `LOGOUT`
    

---

## 2. POP3 (Post Office Protocol v3)

- Used to **retrieve emails only**
    
- Emails usually **downloaded and removed from server**
    

### Features

- Simple protocol
    
- No folder support
    
- No synchronization
    
- Limited functionality
    

### Working

- Ports:
    
    - **110 → normal**
        
    - **995 → SSL/TLS**
        
- Basic commands only
    

### Example Commands

- `USER username`
    
- `PASS password`
    
- `STAT` → count emails
    
- `LIST` → list emails
    
- `RETR id` → retrieve mail
    
- `DELE id` → delete mail
    
- `QUIT`
    

---

## 3. IMAP vs POP3 (Important Difference)

|Feature|IMAP|POP3|
|---|---|---|
|Storage|Server|Local|
|Sync|Yes|No|
|Folder support|Yes|No|
|Multi-device access|Yes|No|
|Email management|Advanced|Basic|

---

## 4. Security in IMAP/POP3

- Default → **unencrypted (plaintext)**
    
- Risk: username/password leakage
    
- Solution → **SSL/TLS encryption**
    
    - IMAP → 993
        
    - POP3 → 995
        

---

## 5. Dangerous Configuration Settings

Misconfigurations can leak sensitive data:

- `auth_debug` → logs authentication process
    
- `auth_debug_passwords` → logs passwords
    
- `auth_verbose` → logs failed logins
    
- `auth_verbose_passwords` → shows passwords
    
- `auth_anonymous_username` → allows anonymous login
    

⚠️ Risk: attacker can read emails or steal credentials

---

## 6. Footprinting (Reconnaissance)

### Common Ports

- POP3 → 110, 995
    
- IMAP → 143, 993
    

### Nmap Scan

```
nmap -sV -p110,143,993,995 <target>
```

### Info Gathered

- Service version (e.g., Dovecot)
    
- SSL certificate details
    
- Organization info
    
- Supported commands (capabilities)
    

---

## 7. Accessing Mail Server

### Using cURL

```
curl -k 'imaps://IP' --user username:password
```

Output example:

```
* LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." INBOX
```

👉 Means:

- Mailboxes available: **Important, INBOX**
    

---

### Using OpenSSL (Encrypted Connection)

#### POP3

```
openssl s_client -connect IP:995
```

#### IMAP

```
openssl s_client -connect IP:993
```

Shows:

- TLS version (e.g., TLS 1.3)
    
- Cipher used
    
- Certificate details
    
- Server banner
    

---

## 8. Attack Scenario (Important)

If credentials found:

```
robin : robin
```

Attacker can:

- Login to IMAP/POP3
    
- Read emails
    
- Send emails
    
- Access sensitive info
    

---

## 9. Key Points (Quick Revision)

- IMAP = **full control + sync**
    
- POP3 = **download only**
    
- Secure ports → **993 (IMAP), 995 (POP3)**
    
- Misconfigurations → **data leakage**
    
- Tools:
    
    - Nmap → scan
        
    - cURL → interact
        
    - OpenSSL → secure connection testing
        

---
 Q1. ### Figure out the exact organization name from the IMAP/POP3 service and submit it as the answer.
 ```
 nmap -sV -p110,143,993,995 --script ssl-cert 10.129.50.164
 ```
 InlaneFreight Ltd

Q2 ### What is the FQDN that the IMAP and POP3 servers are assigned to?
- dev.inlanefreight.htb


Q3 ### Enumerate the IMAP service and submit the flag as the answer. (Format: HTB{...})
```
openssl s_client -connect 10.129.50.164:993
```
- HTB{roncfbw7iszerd7shni7jr2343zhrj}

Q4. 
### What is the customized version of the POP3 server?
```
nc 10.129.50.164 110
```
- InFreight POP3 v9.188

Q5 ### What is the admin email address?
```
openssl s_client -connect 10.129.50.164:993
a LOGIN robin robin
a LIST "" *
a SELECT INBOX
a SELECT DEV.DEPARTMENT.INT
a FETCH 1 BODY[]
```
- devadmin@inlanefreight.htb

Q 6### Try to access the emails on the IMAP server and submit the flag as the answer. (Format: HTB{...})
- HTB{983uzn8jmfgpd8jmof8c34n7zio}
