
# ☁️ Cloud Resources 

## 🔹 What are Cloud Resources?

Cloud platforms like

- Amazon Web Services (AWS)
    
- Google Cloud Platform (GCP)
    
- Microsoft Azure
    

provide:

- Storage
    
- Servers
    
- Networking
    
- Applications
    

👉 Used for **centralized access from anywhere**

---

## 🔹 Why Companies Use Cloud?

- Remote work support
    
- Central data management
    
- Scalability
    
- Cost-effective infrastructure
    

---

## 🔹 Security Reality

✔ Cloud providers secure infrastructure  
❌ But **misconfiguration by admins = vulnerability**

---

## 🔥 Common Cloud Vulnerabilities

### 1. Public Storage Exposure

- AWS → S3 buckets
    
- Azure → Blob storage
    
- GCP → Cloud storage
    

👉 If misconfigured → **no authentication required**

---

### 2. Sensitive Data Leakage

Examples:

- PDFs
    
- Source code
    
- Config files
    
- SSH keys (`id_rsa`)
    

👉 Can lead to **full system compromise**

---

### 3. DNS & Subdomain Exposure

- Cloud services often appear in DNS
    
- Example:
    
    ```
    s3-website-us-west-2.amazonaws.com
    ```
    

👉 Helps attackers identify cloud usage

---

## 🔍 Cloud Discovery Techniques

### 1. Subdomain Enumeration

Command:

```bash
for i in $(cat subdomainlist); do host $i; done
```

👉 Finds:

- Internal servers
    
- Cloud endpoints
    

---

### 2. Google Dorking

Search patterns:

```
inurl:amazonaws.com
inurl:blob.core.windows.net
```

👉 Finds:

- Public cloud files
    
- Documents & backups
    

---

### 3. Website Source Code Analysis

Check:

- JS files
    
- CSS links
    
- Image URLs
    

👉 Often reveals cloud storage links

---

### 4. Third-Party Tools

#### 🔹 Domain.glass

- Infrastructure info
    
- Security status (e.g., Cloudflare protection)
    

#### 🔹 GrayHatWarfare

- Searches public S3 buckets
    
- Filter by file type
    
- Shows exposed files
    

---

## ⚠️ Real Risk Example

- Public S3 bucket contains:
    
    - `id_rsa` (private key)
        
    - `id_rsa.pub`
        

👉 Attacker can:

- Download key
    
- Login via SSH
    
- No password needed
    

---

## 🔐 Causes of Misconfiguration

- Human error
    
- Lack of awareness
    
- Poor access control
    
- Default settings not changed
    

---

## 🛡️ Prevention Methods

### ✔ Access Control

- Use IAM policies
    
- Restrict public access
    

### ✔ Encryption

- Data at rest
    
- Data in transit
    

### ✔ Monitoring

- Logs & alerts
    
- Detect unusual access
    

### ✔ Regular Audits

- Check bucket permissions
    
- Scan for leaks
    

---
