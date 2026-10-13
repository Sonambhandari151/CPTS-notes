
# ✅ 🔹 Server Message Block (SMB)

## 📌 Introduction

- **SMB (Server Message Block)** is a **client-server protocol**
    
- Used for:
    
    - File sharing
        
    - Directory access
        
    - Network resource sharing
        

---

# 🎯 🔹 Purpose of SMB

- Access:
    
    - Files
        
    - Folders
        
    - Printers
        
    - Network devices (routers, interfaces)
        
- Also used for:
    
    - Communication between system processes
        

---

# 🖥️ 🔹 Where SMB is Used

|Platform|Usage|
|---|---|
|Windows|Native support (main use)|
|Linux/Unix|Via Samba|
|Cross-platform|Windows ↔ Linux communication|

---

# 📜 🔹 History

|Stage|Description|
|---|---|
|OS/2 LAN Manager|Early SMB implementation|
|LAN Server|Extended usage|
|Windows OS|Widely adopted|
|Modern SMB|Backward compatible|

---

# 🔄 🔹 Backward Compatibility

👉 SMB supports:

- Old + new systems communication
### ✔ Meaning:

- New Windows can talk to old Windows systems
    
---

- SMB allows a **client to access shared files/services** from other systems in the same network
    
- Both systems must support SMB and use an **SMB server application**
    
- Before communication, **connection is established by exchanging messages**
    

---

## 🔗 Connection

- Uses **TCP protocol**
    
- Follows **three-way handshake** before communication
    
- TCP handles **data transfer after connection**
    

---

## 📂 Shares

- Server shares **selected parts of its file system**
    
- Client view is **different from actual server structure**
    

---

## 🔐 Access Control (ACL)

- Permissions controlled using **ACL (Access Control Lists)**
    
- Fine-grained permissions:
    
    - Read
        
    - Execute
        
    - Full access
        
- Based on:
    
    - Users
        
    - Groups
        

👉 Important:

- **ACLs apply to shares, not local server permissions**
    

---
## Samba

- **Samba**: SMB server for **Unix-based systems**
    
- Implements **CIFS** (Common Internet File System), a **SMB dialect**
    
- Enables communication with **Windows systems** → also called **SMB/CIFS**
    

---

## 🔗 Protocol & Ports

- **CIFS ≈ SMB version 1**
    
- Older NetBIOS connections: **TCP ports 137, 138, 139**
    
- CIFS direct: **TCP port 445**
    

---

## 📌 SMB Versions

- **SMB 1 / CIFS** → outdated
    
- **SMB 2 & SMB 3** → modern, improved, preferred
    
    
- CIFS = SMB v1, communicates with Windows
    
| **SMB Version** | **Supported**                       | **Features**                                                           |
| --------------- | ----------------------------------- | ---------------------------------------------------------------------- |
| CIFS            | Windows NT 4.0                      | Communication via NetBIOS interface                                    |
| SMB 1.0         | Windows 2000                        | Direct connection via TCP                                              |
| SMB 2.0         | Windows Vista, Windows Server 2008  | Performance upgrades, improved message signing, caching feature        |
| SMB 2.1         | Windows 7, Windows Server 2008 R2   | Locking mechanisms                                                     |
| SMB 3.0         | Windows 8, Windows Server 2012      | Multichannel connections, end-to-end encryption, remote storage access |
| SMB 3.0.2       | Windows 8.1, Windows Server 2012 R2 |                                                                        |
| SMB 3.1.1       | Windows 10, Windows Server 2016     | Integrity checking, AES-128 encryption                                 |

- Samba has two main daemons(bg programs).
- **SMBD**: 1st 2 functionalities , **file sharing** and **printer sharing.**
- **NMBD**: Last 2 functionalities, **NetBIOS name service** and **Browsing**.
- **Samba**
    
    - Open-source implementation of SMB/CIFS
        
    - Allows **Linux ↔ Windows** file & printer sharing
        
- **Workgroup**
    
    - Logical group of computers in SMB network
        
    - Each system belongs to a workgroup
        
    - Multiple workgroups can exist in same network
        
- **NetBIOS (API)**
    
    - Developed by IBM for network communication
        
    - Provides interface for applications to share data between systems
        
- **Name Registration**
    
    - Every machine needs a **unique NetBIOS name** when joining network
        
    - Done by:
        
        - Self-registration (host reserves its name), or
            
        - Using **NBNS (NetBIOS Name Server)**
            
- **WINS**
    
    - Enhanced version of NBNS by Microsoft
        
    - Resolves NetBIOS names to IP addresses
        
---
## Default config
|**Setting**|**Description**|
|---|---|
|`[sharename]`|The name of the network share.|
|`workgroup = WORKGROUP/DOMAIN`|Workgroup that will appear when clients query.|
|`path = /path/here/`|The directory to which user is to be given access.|
|`server string = STRING`|The string that will show up when a connection is initiated.|
|`unix password sync = yes`|Synchronize the UNIX password with the SMB password?|
|`usershare allow guests = yes`|Allow non-authenticated users to access defined share?|
|`map to guest = bad user`|What to do when a user login request doesn't match a valid UNIX user?|
|`browseable = yes`|Should this share be shown in the list of available shares?|
|`guest ok = yes`|Allow connecting to the service without using a password?|
|`read only = yes`|Allow users to read files only?|
|`create mask = 0700`|What permissions need to be set for newly created files?|
## Dangerous settings
| **Setting**                 | **Description**                                                     |
| --------------------------- | ------------------------------------------------------------------- |
| `browseable = yes`          | Allow listing available shares in the current share?                |
| `read only = no`            | Forbid the creation and modification of files?                      |
| `writable = yes`            | Allow users to create and modify files?                             |
| `guest ok = yes`            | Allow connecting to the service without using a password?           |
| `enable privileges = yes`   | Honor privileges assigned to specific SID?                          |
| `create mask = 0777`        | What permissions must be assigned to the newly created files?       |
| `directory mask = 0777`     | What permissions must be assigned to the newly created directories? |
| `logon script = script.sh`  | What script needs to be executed on the user's login?               |
| `magic script = script.sh`  | Which script should be executed when the script gets closed?        |
| `magic output = script.out` | Where the output of the magic script needs to be stored?            |
- Adjust services at /etc/samba/smb.conf , then restart
```
sudo systemctl restart smbd
```

---
## Connecting to share
```
smbclient -N -L //10.129.14.128
```
- **-L:** List all shared resources.
- **-N:** null session, anonymous login with no password.
## To access a share
```
smbclient //ip/share
```

## To get a file : get
## To execute local system commands : (!cmd)

---
### **1. `smbstatus` (Admin Tool)**

- Used to **check active SMB connections**
    
- Shows:
    
    - Connected **users**
        
    - Their **IP/host**
        
    - Which **share** they are using
        
    - Samba **version**
        

👉 Useful for monitoring activity inside a network/subnet

---

### **2. Output Explanation**

**Samba version**

- `4.11.6-Ubuntu` → version of Samba running
    

**Connection Table**

- `PID` → Process ID of connection
    
- `Username` → Logged-in user (e.g., `sambauser`)
    
- `Machine` → Client IP (e.g., `10.10.14.4`)
    
- `Protocol` → SMB version (SMB3_11)
    

**Service Section**

- `Service` → Share name (e.g., `notes`)
    
- `Connected at` → Time of connection
    

👉 Means:  
User **sambauser** from IP `10.10.14.4` is connected to **notes share**

---

### **3. Domain-Level Security**

- Samba server joins a **Windows domain**
    
- Uses a **Domain Controller (DC)** for authentication
    

**Domain Controller does:**

- Stores users & passwords in:
    
    - **NTDS.dit**
        
    - **SAM**
        
- Authenticates users when they:
    
    - Login
        
    - Access shared resources
        

---

### **1. Footprinting SMB Service**

- Goal: **Gather maximum info about SMB/Samba server**
    
- Tools:
    
    - **Nmap** → basic scan
        
    - Manual tools → deeper enumeration (more useful)
        

---

### **2. Nmap Scan**

```bash
nmap 10.129.14.128 -sV -sC -p139,445
```

**Findings:**

- Ports:
    
    - `139` → NetBIOS
        
    - `445` → SMB
        
- Service:
    
    - Samba `4.6.2`
        
- Info:
    
    - NetBIOS name: `HTB`
        
    - SMB signing: enabled (not required)
        

👉 **Limitation:**

- Only **basic info**, not detailed enumeration
    

---

### **3. RPC (Remote Procedure Call)**

- Used for **client-server communication**
    
- Allows:
    
    - Sending requests
        
    - Receiving responses (functions execution)
        

---

### **4. rpcclient Tool**

```bash
rpcclient -U "" 10.129.14.128
```

- `-U ""` → anonymous login
    

👉 Gives **deep SMB info via RPC queries**

---

### **5. Important rpcclient Commands**

|Command|Purpose|
|---|---|
|`srvinfo`|Server info|
|`enumdomains`|List domains|
|`querydominfo`|Domain details|
|`netshareenumall`|List shares|
|`netsharegetinfo <share>`|Share details|
|`enumdomusers`|List users|
|`queryuser <RID>`|User details|

---

### **6. Key Enumeration Results**

#### **Server Info**

- OS version: `6.1` → Windows 7 / Server 2008 R2
    

#### **Domains**

- `DEVSMB`
    
- `Builtin`
    

#### **Domain Info**

- Domain: `DEVOPS`
    
- Users: `2`
    
- Role: **Primary Domain Controller (PDC)**
    

---

### **7. Share Enumeration**

Shares found:

- `print$` → printers
    
- `home` → user directory
    
- `dev` → dev folder
    
- `notes` → important share
    
- `IPC$` → system communication
    

👉 `notes` share:

- Path: `C:\mnt\notes\`
    
- Permissions: **Full access (0x1ff)**
    

---

### **8. User Enumeration**

```bash
enumdomusers
```

Users:

- `mrb3n`
    
- `cry0l1t3`
    

---

### **9. User Details**

```bash
queryuser <RID>
```

Info includes:

- Username
    
- Home directory
    
- Password last set
    
- Login times
    
- Group ID
    

---

### **10. Group Info**

```bash
querygroup 0x201
```

- Group: **Ordinary Users**
    
- Members: `2`
    

---

### **11. RID Brute Forcing**

- RID = **Relative Identifier (user ID)**
    

```bash
for i in $(seq 500 1100); do ...
```

👉 Finds:

- Hidden users
    
- Unknown accounts
    

---

### **12. Impacket Tool**

```bash
samrdump.py 10.129.14.128
```

- Dumps:
    
    - Users
        
    - UID
        
    - Password info
        
    - Account status
        

---

### **13. SMBMap**

```bash
smbmap -H 10.129.14.128
```

- Shows:
    
    - Shares
        
    - Permissions
        

👉 Result:

- All shares → **NO ACCESS**
    

---

### **14. CrackMapExec**

```bash
crackmapexec smb 10.129.14.128 --shares -u '' -p ''
```

- Shows:
    
    - OS info
        
    - SMB version
        
    - Share permissions
        

👉 Key finding:

- `notes` → **READ, WRITE access**
    

---

### **15. Enum4Linux-ng**

```bash
./enum4linux-ng.py 10.129.14.128 -A
```

👉 Automated tool (combines many techniques)

---

### **16. Important Findings from Enum4Linux**

**Access:**

- Anonymous login allowed ✅
    

**System Info:**

- OS: Windows 7 / Server 2008 R2
    
- Workgroup: `DEVOPS`
    

**SMB Versions:**

- SMB1 → ❌
    
- SMB2/3 → ✅
    

**Users:**

- `mrb3n`
    
- `cry0l1t3`
    

**Shares:**

- `home`, `notes` → accessible
    

**Password Policy:**

- Min length: `5`
    
- Complexity: ❌ (weak security)
    

---

### **17. Security Risks Identified**

- Anonymous access enabled ❌
    
- Weak password policy ❌
    
- User enumeration possible ❌
    
- Share access (read/write) ❌
    

👉 Leads to:

- Brute-force attacks
    
- Data leakage
    
- Full network compromise
    

---

### **18. Key Concept**

👉 **Never rely on one tool**

- Different tools → different results
    
- Manual enumeration → more accurate
    

---
  

## Question 1

### What version of the SMB server is running on the target system? Submit the entire banner as the answer.
-  nmap 10.129.41.74 -sV -p139,445

# Q2
### What is the name of the accessible share on the target?
- smbclient -N -L //10.129.41.74

## Q3
### Connect to the discovered share and find the flag.txt file. Submit the contents as the answer.
- smbclient //10.129.41.74/sambashare -N

## Q4
Find out which domain the server belongs to.
- rpcclient -U "" 10.129.41.74
- querydominfo

## Q5
### Find additional information about the specific share we found previously and submit the customized version of that specific share as the answer.
- rpcclient -U "" 10.129.41.74 -c "netsharegetinfo sambashare"

## Q6
### What is the full system path of that specific share? (format: "/directory/names")
- rpcclient -U "" 10.129.41.74 -c "netsharegetinfo sambashare"