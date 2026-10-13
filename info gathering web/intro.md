![[Pasted image 20260408150027.png]]

### Primary goals of web recon
1. **Identifying Assets:**  Uncovering all publicly accessible components of the target, such as web pages, subdomains, IP addresses, and technologies used. This step provides a comprehensive overview of the target's online presence.
2. **Discovering Hidden Information:** Locating sensitive information that might be inadvertently exposed, including backup files, configuration files, or internal documentation. These findings can reveal valuable insights and potential entry points for attacks.
3. **Analysing the Attack surface:** xamining the target's attack surface to identify potential vulnerabilities and weaknesses. This involves assessing the technologies used, configurations, and possible entry points for exploitation.
4. **Gathering Intelligence:** Collecting information that can be leveraged for further exploitation or social engineering attacks. This includes identifying key personnel, email addresses, or patterns of behaviour that could be exploited.
---

# 📘 **Types of Web Reconnaissance**

## 🔹 1. Active Reconnaissance

👉 Direct interaction with target system

### ✅ Key Points

- Attacker sends requests to target
    
- More **detailed information**
    
- **High risk of detection**
    

### 🔧 Techniques

- **Port Scanning** → find open ports (e.g., 80, 443)
    
- **Vulnerability Scanning** → find security flaws
    
- **Network Mapping** → identify network structure
    
- **Banner Grabbing** → get service info
    
- **OS Fingerprinting** → detect OS
    
- **Service Enumeration** → find service versions
    
- **Web Spidering** → crawl website
    

### ⚠️ Risk

👉 Medium to High (can trigger IDS/firewall)

---

## 🔹 2. Passive Reconnaissance

👉 No direct interaction with target

### ✅ Key Points

- Uses **publicly available data**
    
- **Stealthy (low detection)**
    
- Limited information
    

### 🔧 Techniques

- **Search Engines** → Google dorking
    
- **WHOIS Lookup** → domain info
    
- **DNS Analysis** → subdomains, MX records
    
- **Web Archives** → old website data
    
- **Social Media** → employee info
    
- **Code Repositories** → leaked credentials
    

### ⚠️ Risk

👉 Very Low

---

