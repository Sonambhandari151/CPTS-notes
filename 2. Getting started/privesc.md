resource : [HackTricks](https://book.hacktricks.xyz) , [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
## Enumeration Scripts:
- Linux : [LinEnum](https://github.com/rebootuser/LinEnum.git) and [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker), 
- WIndows: [Seatbelt](https://github.com/GhostPack/Seatbelt) and [JAWS](https://github.com/411Hall/JAWS).
- Server Enumeration : [Privilege Escalation Awesome Scripts SUITE (PEASS)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) (Both windows, linux). EG : LinPEAS.

### Kernal Exploit
- Look for server running on old operating systems. 
- EG: Use searchsploit for 3.9.0-73-generic , CVE-2016-5195 ( Dirtycow).

---
## Vulnerable Software
- To check software installed on system. 
- Linux : **dpkg -l**.
- Windows: **C:\Program Files**.
- Look for public exploits for any installed software.
---
## User Privileges
- To run specific commands as root. 
     - **sudo** 
         [GTFOBins](https://gtfobins.github.io) (sudo -l)
         [LOLBAS](https://lolbas-project.github.io/#) Windows
     - **SUID**
     - **Windows Token Privileges.**
---
## Scheduled Tasks

	1. Add new scheduled tasks/cron jobs
    2. Trick them to execute a malicious software
- If we have write permissions , we can add new cron jobs to :   
     1. `/etc/crontab`
    2. `/etc/cron.d`
    3. `/var/spool/cron/crontabs/root`

---
## Exposed Credentials
- Look for files we can read , if they contain any exposed credentials.
- Config files, log files, user history files ( **bash_history** in linux, **PSReadLine** in Windows.)

---

## 🔑 SSH Keys 

### 1. **If we have _read_ access**

- Check for private keys:
    
    - `/home/user/.ssh/id_rsa`
        
    - `/root/.ssh/id_rsa`
        
- Copy the private key locally, save as `id_rsa`.
    
- Fix permissions:
    
    ```bash
    chmod 600 id_rsa
    ```
    
- Use it to log in:
    
    ```bash
    ssh user@IP -i id_rsa
    ```
    
- ⚠️ SSH enforces strict permissions; world-readable keys won’t work.
    

---

### 2. **If we have _write_ access**

- Generate our own key pair locally:
    
    ```bash
    ssh-keygen -f key
    ```
    
    - `key` → private key (we keep).
        
    - `key.pub` → public key (we copy).
        
- Append our public key to target’s `authorized_keys`:
    
    ```bash
    echo "ssh-rsa AAAAB... user@attacker" >> /home/user/.ssh/authorized_keys
    ```
    
- Now we can log in without password:
    
    ```bash
    ssh user@IP -i key
    ```
    

---

### 3. **Key Differences**

- **Read access** → steal existing key (impersonate the real user).
    
- **Write access** → insert our own key (backdoor persistent access).
    

---
