
# ✅ 🔹 Domain Information (Core Idea)

- Part of **reconnaissance in penetration testing**
    
- Focus = **complete online presence**, not just subdomains
    
- Done using **passive methods (OSINT)** → no direct interaction
    
- Goal: understand **company structure, services, technologies**
    

---

# ✅ 🔹 Passive Information Gathering

- No direct scanning (stay hidden as normal user)
    
- Sources:
    
    - Company website
        
    - SSL certificates
        
    - Third-party services
        

👉 Helps identify:

- Tech stack
    
- Infrastructure
    
- Possible attack surface
    

---

# ✅ 🔹 SSL Certificate Analysis

- Contains:
    
    - Domain names
        
    - Subdomains
        
- Example:
    
    - `www.domain.com`
        
    - `support.domain.com`
        

👉 Used to discover **hidden subdomains**

---

# ✅ 🔹 Certificate Transparency (crt.sh)

- Public logs of SSL certificates
    
- Used to find:
    
    - Subdomains
        
    - Old domains
        

👉 Command:

```bash
curl -s https://crt.sh/?q=domain.com&output=json
```

---

# ✅ 🔹 Subdomain Enumeration

- Extract unique subdomains using tools like:
    
    - `jq`, `grep`, `sort`
        

👉 Helps map:

- Internal structure
    
- Entry points
    

---

# ✅ 🔹 Identify Company-Hosted Servers

- Filter IPs belonging to company (not third-party)
    
- Important because:
    
    - Only these are **in-scope targets**
        

---

# ✅ 🔹 Shodan Usage

- Search engine for **Internet-connected devices**
    

👉 Finds:

- Open ports
    
- Services (HTTP, SSH, FTP)
    
- Server details
    

👉 Example findings:

- Ports: 80, 443 → Web server
    
- Port 22 → SSH access
    

---

# ✅ 🔹 DNS Enumeration (`dig`)

```bash
dig any domain.com
```

### Important Records:

|Record|Meaning|
|---|---|
|A|Domain → IP|
|MX|Mail servers|
|NS|Name servers|
|TXT|Verification / security|
|SOA|Domain authority|

---

# ✅ 🔹 TXT Records (Very Important)

Contain:

- Verification tokens
    
- Security configs
    

👉 Examples reveal:

- Google (Gmail)
    
- Atlassian
    
- Mailgun
    
- Outlook
    
- LogMeIn
    

---

# ✅ 🔹 Technology Insights from DNS

|Service|Meaning|
|---|---|
|Atlassian|Dev & collaboration tools|
|Gmail|Email system|
|Mailgun|Email API|
|Outlook|Cloud storage (OneDrive)|
|LogMeIn|Remote access|
|INWX|Domain provider|

---

# ✅ 🔹 Security Insights

- Centralized services = **high-value targets**
    
- Possible attack areas:
    
    - APIs → (IDOR, SSRF)
        
    - Cloud storage → exposed files
        
    - Email systems → phishing / misconfig
        

---

# ✅ 🔹 Key Pentesting Approach

1. Analyze website
    
2. Extract domains/subdomains
    
3. Identify IPs
    
4. Use Shodan
    
5. Analyze DNS records
    
6. Map technologies
    

---
