# 📘 **Subdomains**

👉 Subdomain = **child domain of main domain**

### 🎯 Example

```text
blog.example.com
shop.example.com
mail.example.com
```

👉 Structure:

```text
subdomain.domain.com
```

---

# 🔥 **Why Subdomains Matter (Recon)**

|Area|What you find|Risk|
|---|---|---|
|Dev/Staging|test.dev.example.com|Weak security|
|Admin Panels|admin.example.com|Login brute-force|
|Legacy Apps|old.example.com|Vulnerable software|
|APIs|api.example.com|Data exposure|
|Internal Tools|internal.example.com|Sensitive data|

---

# 🔎 **Subdomain Enumeration**

👉 Process of **finding all subdomains**

---

# ⚙️ **Types of Enumeration**

## 1️⃣ Active Enumeration

👉 Direct interaction with target DNS

|Method|Description|Tools|
|---|---|---|
|Zone Transfer|Get full DNS records (rare)|`dig AXFR`|
|Brute Force|Try wordlist (admin, dev, test)|dnsenum, gobuster, ffuf|

### 📌 Example

```bash
gobuster dns -d example.com -w wordlist.txt
```

---

## 2️⃣ Passive Enumeration

👉 No direct interaction (stealthy)

|Method|Source|Example|
|---|---|---|
|CT Logs|SSL certificates|crt.sh|
|Search Engines|Google dork|site:example.com|
|OSINT Tools|Aggregated data|theHarvester|
|DNS Databases|Historical data|SecurityTrails|

---

# 🔁 **Active vs Passive (Important Table)**

|Feature|Active|Passive|
|---|---|---|
|Interaction|Direct|Indirect|
|Detection Risk|High|Low|
|Coverage|More|Limited|
|Speed|Slower|Faster|
|Stealth|No|Yes|

---

# 🔧 **DNS Records Used in Subdomains**

|Record|Role in Subdomain|
|---|---|
|A|Maps subdomain → IP|
|AAAA|IPv6 mapping|
|CNAME|Alias → another domain|
|MX|Mail subdomain|
|TXT|Info / verification|

---

# 🧠 **Important Commands**

```bash
dig sub.example.com
```

```bash
dig example.com ANY
```

```bash
dig AXFR example.com @ns1.example.com
```

---

# 🔐 **Real Recon Strategy (Step-by-Step)**

1. Get main domain info
    
2. Find NS records
    
3. Try zone transfer
    
4. Run brute-force tool
    
5. Check CT logs
    
6. Combine results
    

---

# ⚠️ **Important Points**

- Zone transfer **rarely works**
    
- Subdomains may be:
    
    - Hidden
        
    - Misconfigured
        
    - Unsecured
        
- Always **combine multiple methods**
    

---

