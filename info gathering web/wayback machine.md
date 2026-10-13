## 📌 Wayback Machine – Quick Notes (HTB/Recon)

### 🔹 What it is

- Internet Archive’s tool
    
- Wayback Machine stores **old versions of websites (since 1996)**
    

---

### 🔹 How it works

1. **Crawling** → bots visit websites
    
2. **Archiving** → save full page (HTML, JS, images)
    
3. **Accessing** → user selects date to view snapshot
    

---

### 🔹 Why important in Recon (VERY IMPORTANT 🔥)

- Find **old endpoints**
    
    - `/admin`, `/backup`, `/dev`
        
- Discover **hidden subdomains**
    
- Leak **sensitive info**
    
    - API keys, emails, comments
        
- Identify **old technologies / vulnerabilities**
    
- Passive → **no direct interaction with target**
    

---

### 🔹 What to look for

- Old URLs
    
- JS files
    
- Comments
    
- Backup files (`.zip`, `.bak`)
    
- Deprecated APIs
    

---

### 🔹 Example usage

```bash
waybackurls inlanefreight.com
```

---
### How many Pen Testing Labs did HackTheBox have on the 8th August 2018? Answer with an integer, eg 1234.
- 74



### How many members did HackTheBox have on the 10th June 2017? Answer with an integer, eg 1234.
- 3054

### Going back to March 2002, what website did the facebook.com domain redirect to? Answer with the full domain, eg http://www.facebook.com/
- http://site.aboutface.com/

### According to the paypal.com website in October 1999, what could you use to "beam money to anyone"? Answer with the product name, eg My Device, remove the ™ from your answer.
- Palm 0rganizer


### Going back to November 1998 on google.com, what address hosted the non-alpha "Google Search Engine Prototype" of Google? Answer with the full address, eg http://google.com
- http://google.stanford.edu/

### Going back to March 2000 on www.iana.org, when exacty was the site last updated? Answer with the date in the footer, eg 11-March-99
- **17-December-99**

### According to wikipedia.com snapshot taken on February 9, 2003, how many articles were they already working on in the English version? Answer with the number they state without any commas, e.g., 100000, not 100,000.
- 104155