
## 🔹 1. What are Virtual Hosts?

- Technique used by web servers (Apache, Nginx, IIS)
    
- Allows **multiple websites on a single IP address**
    

👉 Server decides which site to serve using **HTTP Host header**

---

## 🔹 2. Subdomain vs VHost (Important Difference)

|Feature|Subdomain|Virtual Host|
|---|---|---|
|Defined in|DNS|Web server config|
|Example|blog.example.com|dev.example.com (may not exist in DNS)|
|Needs DNS record|✅ Yes|❌ Not always|
|Discoverable via DNS|✅ Yes|❌ No (hidden possible)|

👉 Key Point:  
**All subdomains can be VHosts, but not all VHosts are in DNS**

---

## 🔹 3. How VHosts Work (Flow)

1. User enters domain → browser sends request
    
2. Request includes **Host header**
    
    ```http
    Host: dev.inlanefreight.htb
    ```
    
3. Server checks config
    
4. Matches correct virtual host
    
5. Returns corresponding website
    

👉 **Host header = switch for website selection**

---

## 🔹 4. Types of Virtual Hosting

### 1. Name-Based (Most Common)

- Uses **Host header**
    
- Multiple domains → same IP
    

✔ Cheap, flexible  
❌ Depends on HTTP support

---

### 2. IP-Based

- Each website → **different IP**
    

✔ Strong isolation  
❌ Expensive, less scalable

---

### 3. Port-Based

- Different websites on different ports
    
    - example: `site1:80`, `site2:8080`
        

✔ Works with limited IPs  
❌ Not user-friendly

---

## 🔹 5. Hidden VHosts (Important for CTF)

- Not present in DNS
    
- Only configured on server
    
- Accessible via:
    
    - Host header fuzzing
        
    - `/etc/hosts` mapping
        

👉 Example:

```bash
10.129.60.206 dev.inlanefreight.htb
```

---

## 🔹 6. VHost Discovery (Core Concept)

👉 Since DNS won’t help → brute-force **Host header**

---

## 🔹 7. Tools for VHost Discovery

### 🔸 gobuster (most common)

```bash
gobuster vhost -u http://<IP> -w <wordlist> --append-domain
```

Example:

```bash
gobuster vhost -u http://10.129.60.206 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

---

### 🔸 Other Tools

- `ffuf` → fast & flexible
    
- `feroxbuster` → recursive + fast
    
- `gobuster` → simple + reliable
    

---

## 🔹 8. Important Flags (Gobuster)

- `-u` → target URL (IP)
    
- `-w` → wordlist
    
- `--append-domain` → required (adds `.domain`)
    
- `-t` → threads (speed)
    
- `-k` → ignore SSL errors
    
- `-o` → save output
    

---

## 🔹 9. Example Output

```bash
Found: forum.inlanefreight.htb Status: 200
```

👉 Means:

- Valid virtual host found
    
- Server responded successfully
    

---

## 🔹 10. Key Differences (Exam Focus)

|Concept|DNS Brute Force|VHost Fuzzing|
|---|---|---|
|Uses DNS|✅|❌|
|Uses Host header|❌|✅|
|Finds hidden hosts|❌|✅|
|Needs IP target|❌|✅|

---

## 🔹 11. Attack Flow (Real-world / CTF)

1. Get target IP
    
2. Try DNS enumeration
    
3. If limited results →
    
4. Perform **VHost fuzzing**
    
5. Identify hidden apps (admin, dev, staging)
    

---

## 🔹 12. Risks & Detection

- Generates heavy traffic
    
- Can trigger:
    
    - IDS
        
    - WAF
        

👉 Always need **authorization**

---

### Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "web"? Answer using the full domain, e.g. "x.inlanefreight.htb"
web17611.inlanefreight.htb
- add inlanefreight.htb in /etc/hosts file
```
gobuster vhost -u http://inlanefreight.htb:31152 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain -t 50

```
