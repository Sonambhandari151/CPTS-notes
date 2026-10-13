**NFS (Network File System) – Complete Detailed Notes**

---

## **1. Introduction**

* **NFS** is a distributed file system developed by **Sun Microsystems**
* Allows users to **access remote files like local files**
* Common in **Linux/Unix environments**
* Works on **client–server architecture**
* Not directly compatible with SMB (used in Windows)

---

## **2. Working Concept**

* A server **exports (shares)** a directory
* A client **mounts** that directory
* After mounting:

  * Remote files behave like local files
  * Users can read/write based on permissions

---

## **3. Architecture**

* Based on **ONC-RPC (Open Network Computing RPC / SUN-RPC)**
* Components:

  * **NFS Server** → shares directories
  * **NFS Client** → accesses shared directories
  * **RPC Portmapper** → maps services to ports

---

## **4. Ports & Communication**

* **Port 111 (TCP/UDP)** → RPC (portmapper)
* **Port 2049 (TCP/UDP)** → NFS service
* Uses **XDR (External Data Representation)**:

  * Ensures platform-independent data exchange

✔️ In **NFSv4**:

* Only **port 2049** is used → easier firewall configuration

---

## **5. NFS Versions (Detailed)**

### **NFSv2**

* Old version
* Uses **UDP**
* Limited file size
* Stateless

---

### **NFSv3**

* Improvements:

  * Larger file sizes
  * Better error handling
  * Performance improvements
* Still **stateless**
* Not fully backward compatible with v2

---

### **NFSv4**

* Major upgrade:

  * **Stateful protocol**
  * Requires **user authentication**
  * Supports **Kerberos security**
  * Works over Internet/firewalls
  * Supports **ACLs (Access Control Lists)**
  * No need for **portmapper**
  * Uses **single port (2049)**

---

### **NFSv4.1**

* Enhancements:

  * **pNFS (Parallel NFS)** → multiple servers → high performance
  * **Multipathing (session trunking)** → multiple network paths
  * Better for **cluster environments**

---

## **6. Stateless vs Stateful**

* **Stateless (v2, v3)**:

  * Server does not track client state
  * Each request independent
* **Stateful (v4)**:

  * Server tracks sessions, locks, open files
  * Better performance & security

---

## **7. Authentication & Authorization**

### **Authentication**

* NFS itself does **NOT provide authentication**
* Delegated to **RPC options**
* Methods:

  * AUTH_SYS (UID/GID based)
  * Kerberos (in NFSv4)

---

### **Authorization**

* Based on:

  * File permissions (UNIX)
  * UID/GID mapping

---

### **Major Issue**

* Client and server may have:

  * Different UID/GID mappings
* Example:

  * UID 1000 = user A (client)
  * UID 1000 = user B (server)

➡️ Leads to **security risks**

✔️ Hence:

* Use NFS only in **trusted environments**

---

## **8. Important Security Concept**

### **root_squash**

* Maps root user to anonymous user
* Prevents remote root from full access

### **Why needed?**

* Without it:

  * Remote root can control server files

---

## **9. Configuration File – `/etc/exports`**

* Main file for sharing configuration
* Defines:

  * Directory to share
  * Allowed clients
  * Permissions

---

### **Syntax**

```id="a1"
/directory  client(options)
```

---

### **Example**

```id="a2"
/mnt/nfs 10.129.14.0/24(sync,no_subtree_check)
```

✔️ Meaning:

* Share `/mnt/nfs`
* Allow subnet `10.129.14.0/24`
* Apply given options

---

## **10. Export Options (Detailed)**

| Option               | Meaning                             |
| -------------------- | ----------------------------------- |
| **rw**               | Read & write access                 |
| **ro**               | Read-only                           |
| **sync**             | Writes immediately (safe, slow)     |
| **async**            | Delayed write (fast, risky)         |
| **secure**           | Only ports <1024 allowed            |
| **insecure**         | Allows ports >1024                  |
| **no_subtree_check** | Skips directory validation (faster) |
| **root_squash**      | Maps root to anonymous user         |
| **no_root_squash**   | Root gets full access (dangerous)   |

---

## **11. Commands (Server Side)**

### Add share:

```id="a3"
echo '/mnt/nfs 10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
```

### Restart service:

```id="a4"
systemctl restart nfs-kernel-server
```

### Show exports:

```id="a5"
exportfs
```

---

## **12. Mounting (Client Side)**

```id="a6"
mount -t nfs <server_ip>:/mnt/nfs /local/mount
```

✔️ After mounting:

* `/local/mount` behaves like local folder

---

- **111 (rpcbind)** → maps RPC services
    
- **2049 (NFS)** → main file sharing service
    

✔️ These two ports are **must-scan** for NFS enumeration

---

# **2. Initial Scanning (Service Discovery)**

### Command:

```bash
nmap <target> -p111,2049 -sV -sC
```

### What happens:

- Detects:
    
    - NFS service
        
    - RPC services via `rpcinfo`
        
- Lists:
    
    - Service versions
        
    - Running RPC programs
        

---

### Key Output Understanding:

- **rpcbind (100000)** → service mapper
    
- **nfs (100003)** → file sharing
    
- **mountd (100005)** → mount requests
    
- **nlockmgr (100021)** → file locking
    
- **nfs_acl (100227)** → access control
    

✔️ **Insight:**  
RPC output tells:

- Which services exist
    
- Which ports they use
    

---

# **3. Advanced Enumeration (NFS NSE Scripts)**

### Command:

```bash
nmap --script nfs* <target> -p111,2049 -sV
```

---

## **What Each Script Does**

### **1. nfs-ls**

- Lists files inside NFS share
    
- Shows:
    
    - Permissions
        
    - UID/GID
        
    - File names
        

✔️ Example finding:

- `id_rsa` → sensitive SSH private key
    

---

### **2. nfs-showmount**

- Displays exported shares
    

```bash
/mnt/nfs 10.129.14.0/24
```

✔️ Meaning:

- `/mnt/nfs` is shared
    
- Accessible by subnet
    

---

### **3. nfs-statfs**

- Shows:
    
    - Disk usage
        
    - Available space
        
    - File system info
        

---

### **4. rpcinfo (again)**

- Confirms all RPC services and ports
    

---

# **4. Manual Share Enumeration**

### Command:

```bash
showmount -e <target>
```

### Output:

```bash
/mnt/nfs 10.129.14.0/24
```

✔️ Confirms:

- Exported directories
    
- Allowed clients
    

---

# **5. Mounting the NFS Share (Access Phase)**

### Commands:

```bash
mkdir target-NFS
mount -t nfs <target>:/ ./target-NFS/ -o nolock
cd target-NFS
```

---

## **What happens internally**

1. Client contacts **rpcbind (111)**
    
2. Gets NFS service details
    
3. Communicates with **mountd**
    
4. Mounts remote filesystem locally
    

---

✔️ Result:

- Remote files accessible like:
    

```
./target-NFS/mnt/nfs/
```

---

# **6. Exploring the Share**

### View structure:

```bash
tree .
```

### Example files:

- `id_rsa` (private key ⚠️)
    
- `id_rsa.pub`
    
- `nfs.share`
    

✔️ These may lead to **direct system access**

---

# **7. Checking File Ownership**

### Username view:

```bash
ls -l
```

### UID/GID view:

```bash
ls -n
```

---

## **Important Observation**

Example:

```
-rw-r--r-- 1 1000 1000 file
```

✔️ Means:

- UID = 1000
    
- GID = 1000
    

---

# **8. Core Weakness (Very Important)**

- NFS trusts **UID/GID**, not usernames
    
- Server does **no strict verification**
    

---

## **Attack Idea**

- Create same UID locally
    
- Gain same access rights
    

✔️ This is called **UID spoofing**

---

# **9. root_squash Concept**

- If enabled:
    
    - Root → converted to anonymous user
        
    - Cannot modify root-owned files
        
- If disabled (**no_root_squash**):
    
    - Root has full control → **critical vulnerability**
        

---

✔️ Example:

- Cannot edit `backup.sh` if root_squash is ON
    

---

# **10. Privilege Escalation via NFS**

## **Scenario**

- Attacker has SSH access (low user)
    
- NFS share is accessible
    

---

## **Attack Steps**

1. Create malicious shell
    
2. Set **SUID bit**
    
3. Upload to NFS share
    
4. Execute from target system
    

---

## **Why it works**

- Shared filesystem trusts UID/GID
    
- Permissions carry over
    

---

✔️ Result:

- Execute as another user / root
    

---

# **11. Sensitive Data Exposure**

From NFS shares, attacker can find:

- SSH keys (`id_rsa`)
    
- Backup scripts
    
- Config files
    
- Usernames & groups
    

---

✔️ Example:

- Use `id_rsa` → SSH login without password
    

---

# **12. Full Process Flow (Step-by-Step)**

### **1. Scan**

```bash
nmap -p111,2049
```

### **2. Enumerate**

```bash
nmap --script nfs*
```

### **3. List shares**

```bash
showmount -e
```

### **4. Mount share**

```bash
mount -t nfs
```

### **5. Explore**

```bash
ls, tree
```

### **6. Analyze**

- UID/GID
    
- Permissions
    
- Sensitive files
    

### **7. Exploit**

- SSH keys
    
- UID spoofing
    
- SUID shell
    

### **8. Cleanup**

```bash
umount
```

---

# **13. Unmounting (Cleanup)**

```bash
cd ..
umount ./target-NFS
```

✔️ Removes mounted directory safely

---

# **14. Key Security Issues**

- No strong authentication
    
- UID/GID trust problem
    
- Misconfigurations:
    
    - `no_root_squash`
        
    - Open access to subnet
        

---

# **15. Important Exam / Viva Points**

- Ports: **111 & 2049**
    
- Tools:
    
    - `nmap`
        
    - `showmount`
        
    - `mount`
        
- Attacks:
    
    - SSH key leakage
        
    - UID spoofing
        
    - SUID escalation
        

---
## Questions
### Enumerate the NFS service and submit the contents of the flag.txt in the "nfs" share as the answer.
```
nmap 10.129.43.28 -p111,2049 -sV -sC
showmount -e 10.129.43.28
mkdir nfs-target
sudo mount -t nfs 10.129.43.28:/var/nfs ./nfs-target -o nolock
cat nfs-target/flag.txt
```

## Q2
Enumerate the NFS service and submit the contents of the flag.txt in the "nfsshare" share as the answer.
```
mkdir nfsshare-target
sudo mount -t nfs 10.129.43.28:/mnt/nfsshare ./nfsshare-target -o nolock
cd nfsshare-target
ls
```