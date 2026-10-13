## 🔴 HTB Section 10/19 — Latest SQL Vulnerabilities (xp_dirtree) 

---

# 📌 Core Idea

- **xp_dirtree is NOT a vulnerability (no CVE)**
    
- It is an **undocumented MSSQL stored procedure**
    
- Used to list directory contents (local or remote SMB share)
    
- Abuse happens due to **SMB NTLM authentication behavior**
    

---

# 🧠 Main Concept

## 🔹 What xp_dirtree does

- Lists folders inside a given path
    
- Supports:
    
    - folder path
        
    - recursion depth (how deep to scan)
        

Example use:

```sql
EXEC master..xp_dirtree '\\server\share\';
```

---

# 💣 WHY IT IS EXPLOITABLE

## 🔹 Key Idea

- Windows automatically authenticates to SMB shares
    
- It uses **NTLM / NTLMv2 authentication**
    
- So when SQL Server accesses a remote share:
    

👉 It automatically sends NTLM hash

---

# 🔁 ATTACK FLOW (IMPORTANT)

## 🔹 Initiation Phase

|Step|Stage|Explanation|
|---|---|---|
|1|Source|User input triggers xp_dirtree with SMB path|
|2|Process|SQL Server processes folder request|
|3|Privileges|MSSQL service executes request using its account|
|4|Destination|Request goes to SMB server|

---

## 🔹 Hash Stealing Phase

|Step|Stage|Explanation|
|---|---|---|
|5|Source|SMB server receives request|
|6|Process|Folder access is attempted|
|7|Privileges|Windows uses NTLM authentication|
|8|Destination|Attacker-controlled SMB server receives request|

---

# 🔐 RESULT

- NTLMv2 hash of MSSQL service account is captured
    
- Tools used:
    
    - Responder
        
    - Wireshark
        
    - tcpdump
        

---

# 💥 WHAT CAN BE DONE WITH HASH

## Attack possibilities:

- Crack password offline (Hashcat)
    
- SMB relay attack
    
- Lateral movement in network
    
- Privilege escalation on other machines
    

---
