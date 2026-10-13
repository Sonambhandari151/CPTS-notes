- SImple network manager protocol was  created to monitor network devices.
- Handle config tasks and change settings remotely.
-  SNMP-enabled hardware includes routers, switches, servers, IoT devices, and many other devices that can also be queried and controlled using this standard protocol. Thus, it is a protocol for monitoring and managing network devices.
- SNMP also transmits control commands using agents over UDP port `161`
- The client can set specific values in the device and change options and settings with these commands.
- SNMP traps are a feature of the Simple Network Management Protocol.
- They allow a server (device) to send information to a client (monitoring system) without a request.
- In normal SNMP, the client requests data from the server using UDP port 161.
- In SNMP traps, the server initiates communication.
- Trap messages are sent over UDP port 162.
- They are triggered by specific events on the device.
- Common events include system failures, high CPU usage, or network interface issues.
- Traps provide real-time alerts.
- They eliminate the need for constant polling by the client.
- This makes network monitoring more efficient and faster.

---
# MIB
- **MIB (Management Information Base)** is a standardized way to organize and describe SNMP data.
    
- It ensures **compatibility across different vendors and devices**.
    
- A MIB is a **text file** that lists all SNMP-manageable objects of a device.
    
- These objects are arranged in a **hierarchical tree structure**.
    
- Each object in the MIB has an **OID (Object Identifier)**.
    
- The OID acts like a **unique address** to locate specific information on a device.
    
- Along with the OID, the MIB provides:
    
    - Object name
        
    - Data type (e.g., integer, string)
        
    - Access permissions (read/write)
        
    - Description of the object
        
- MIB files are written in **ASN.1 (Abstract Syntax Notation One)** format.
    
- They are stored as **ASCII text files**.
    
- Important:
    
    - MIBs do **not store actual data**.
        
    - They only define:
        
        - Where data is located
            
        - What type of data it is
            
        - How it can be accessed
            
- In simple terms, a MIB is like a **map or blueprint** that tells SNMP tools how to find and understand device information.
---
# OID
- **OID (Object Identifier)** represents a node in a hierarchical structure (tree).
    
- It is used in SNMP to uniquely identify each object or piece of information.
    
- An OID is a **sequence of numbers**.
    
- These numbers show the **exact position of the object in the tree**.
    
- OIDs are written in **dot notation**, for example:
    
    ```
    1.3.6.1.2.1
    ```
    
- The structure is **hierarchical**:
    
    - Each number represents a level in the tree
        
    - The path defines how to reach a specific object
        
- The **longer the OID**, the more specific the information it points to.
    
- Some nodes in the tree:
    
    - Do not contain actual data
        
    - Only act as **branches leading to more specific nodes**
        
- OIDs are linked to **MIB definitions**, which explain:
    
    - What the OID represents
        
    - Data type and access details
        
- You can look up OIDs and their meanings in an **Object Identifier Registry**.
    
- In simple terms, an OID is like a **precise address** that tells SNMP exactly where to find a specific piece of information in a device.
---
# 📌 SNMPv1 (Version 1)

- First version of SNMP
    
- Used for basic network monitoring and management
    
- Can:
    
    - Read device info
        
    - Change configurations
        
    - Send traps (alerts)
        

### ❌ Problems:

- No authentication (no login security)
    
- No encryption
    
- Data sent in **plain text** (easy to sniff)
    

👉 Simple: **Works, but very insecure**

---

# 📌 SNMPv2 (v2c)

- Improved version of SNMPv1
    
- v2c = **community-based** (uses community string like a password)
    

### ✔️ Improvements:

- Better performance
    
- More features
    

### ❌ Problems:

- Community string sent in **plain text**
    
- Still **no encryption**
    
- Security almost same as v1
    

👉 Simple: **Faster, but still insecure**

---

# 📌 SNMPv3 (Version 3)

- Most secure version
    

### ✔️ Features:

- Authentication (username + password)
    
- Encryption (data is protected)
    
- Better access control
    

### ❌ Drawback:

- More complex to configure
    

👉 Simple: **Secure but harder to set up**

---
Here’s a **simple, clear explanation in points**:

---

# 📌 Community Strings (SNMP)

- Community strings are like **passwords** in SNMP
    
- They control **access to device information**
    

---

# 🔑 How they work

- Sent by the client when requesting data
    
- Server checks the community string
    
- If correct → access granted
    
- If wrong → access denied
    

---

# 📂 Types (common)

- **public** → read-only access
    
- **private** → read + write access
    

---

# ⚠️ Problems / Weaknesses

- Sent in **plain text** (no encryption)
    
- Can be **captured using sniffing tools**
    
- Easy to guess (often default values like “public”)
    

---
#### SNMP Daemon Config
```
cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'
```
## Dangerous Settings

|**Settings**|**Description**|
|---|---|
|`rwuser noauth`|Provides access to the full OID tree without authentication.|
|`rwcommunity <community string> <IPv4 address>`|Provides access to the full OID tree regardless of where the requests were sent from.|
|`rwcommunity6 <community string> <IPv6 address>`|Same access as with `rwcommunity` with the difference of using IPv6.|

---
# 📌 Footprinting SNMP

- Used to **gather information from SNMP service**
    
- Works if:
    
    - SNMP is enabled
        
    - Community string is known
        

---

# 🔧 Tools Used

## 1. snmpwalk

- Queries SNMP data using OIDs
    
- Dumps large amount of system info
    

```bash
snmpwalk -v2c -c public <IP>
```

### 🔍 Can reveal:

- OS version
    
- Hostname
    
- Email addresses
    
- Installed software
    
- System uptime
    

---

## 2. onesixtyone

- Used to **brute-force community strings**
    

```bash
sudo apt install onesixtyone 
onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128
```

### 🔍 Purpose:

- Find valid community string (e.g., public)
    

---

## 3. braa

- Fast SNMP scanner
    
- Brute-forces OIDs
    

```bash
braa public@<IP>:.1.3.6.*
```

### 🔍 Purpose:

- Enumerate SNMP data quickly
    

---

# ⚠️ Key Points

- SNMPv1/v2c → no authentication
    
- Community string = weak password
    
- Data is **not encrypted**
    
- Misconfiguration = **data leakage**
    

---

# 💀 Attacker Workflow

1. Find SNMP service (port 161)
    
2. Brute-force community string (onesixtyone)
    
3. Dump data (snmpwalk)
    
4. Enumerate deeper (braa)
    


---
### Enumerate the SNMP service and obtain the email address of the admin. Submit it as the answer.
- snmpwalk -v2c -c public 10.129.53.29
- devadmin@inlanefreight.ht
### What is the customized version of the SNMP server?
- InFreight SNMP v0.91

### Enumerate the custom script that is running on the system and submit its output as the answer.
- HTB{5nMp_fl4g_uidhfljnsldiuhbfsdij44738b2u763g}

