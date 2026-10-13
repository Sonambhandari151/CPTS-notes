## Web Technology Fingerprinting: Comprehensive Technical Notes

Fingerprinting is a core phase of reconnaissance that aims to identify the "Digital DNA" of a web application. This includes the **Web Server**, **Operating System (OS)**, **Programming Languages**, **Frameworks**, and **Content Management Systems (CMS)**.

---

### 1. Objectives of Fingerprinting

- **Vulnerability Mapping:** Matching identified software versions (e.g., Apache 2.4.41) to specific CVEs.
    
- **Attack Tailoring:** Choosing tools specific to the environment (e.g., using `wpscan` only after confirming WordPress).
    
- **Security Posture Assessment:** Identifying missing security headers or misconfigured default files.
    
- **Bypassing Defenses:** Detecting Firewalls (WAFs) early to adjust the "loudness" of the attack.
    

---

### 2. Fingerprinting Techniques & Tools

|**Technique**|**Description**|**Primary Tools**|
|---|---|---|
|**Banner Grabbing**|Analyzing the `Server` or `X-Powered-By` headers in HTTP responses.|`curl`, `telnet`, `nc`|
|**HTTP Header Analysis**|Checking for non-standard headers like `X-Redirect-By` or `Link`.|`curl -I`, browser dev tools|
|**WAF Detection**|Probing the server to identify active security filters.|`wafw00f`|
|**Automated Profiling**|Using signature databases to identify complex stacks.|`WhatWeb`, `Wappalyzer`|
|**Vulnerability Scanning**|Searching for outdated software and misconfigurations.|`Nikto`, `Nmap (NSE)`|

---

### 3. Manual Fingerprinting with `curl`

The `curl -I` command retrieves only the HTTP headers. This is the stealthiest way to grab a server banner.

Bash

```
# 1. Initial check on the main domain
curl -I inlanefreight.com

# 2. Trace the redirect to see if the technology changes
curl -I https://inlanefreight.com

# 3. Check the final destination (usually www)
curl -I https://www.inlanefreight.com
```

**Key Indicators:**

- `Server: Apache/2.4.41 (Ubuntu)` → Reveals both Web Server and OS.
    
- `X-Redirect-By: WordPress` → Confirms the CMS.
    
- `Link: <.../wp-json/>` → Confirms the WordPress REST API is active.
    

---

### 4. WAF Detection with `wafw00f`

Identifying a Web Application Firewall is critical. If you run aggressive scans against a WAF, your IP will be banned.

**Installation:**

Bash

```
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

**Execution:**

Bash

```
wafw00f inlanefreight.com
```

- **Result:** `The site is behind Wordfence (Defiant) WAF.` This tells the attacker that standard WordPress exploits might be actively filtered.
    

---

### 5. Comprehensive Scanning with Nikto

Nikto is a classic web server scanner. For fingerprinting, we use "Tuning" to focus only on software identification.

#### Manual Installation

Bash

```
# Install Perl (Nikto's engine)
sudo apt update && sudo apt install -y perl

# Clone from source
git clone https://github.com/sullo/nikto
cd nikto/program
chmod +x ./nikto.pl

# Verify version
./nikto.pl -Version
```

#### Focused Fingerprinting Scan

Using the `-Tuning b` flag limits the scan to **Software Identification** only.

Bash

```
./nikto.pl -h inlanefreight.com -Tuning b
```

**What Nikto Extracts:**

- **Outdated Components:** Alerts if Apache/PHP versions are EOL (End of Life).
    
- **Insecure Headers:** Detects missing `Strict-Transport-Security` or `X-Content-Type-Options`.
    
- **Sensitive Files:** Locates `license.txt`, `readme.html`, or login portals (`/wp-login.php`).
    

---

### 6. Target Profile Summary: inlanefreight.com

|**Category**|**Finding**|**Security Implication**|
|---|---|---|
|**OS**|Ubuntu Linux|Standard target for Linux-based privilege escalation.|
|**Web Server**|Apache 2.4.41|Outdated; potential vulnerabilities in the 2.4.x branch.|
|**CMS**|WordPress|High attack surface due to plugins and themes.|
|**WAF**|Wordfence|Requires evasion; standard payloads may be blocked.|
|**Vulnerabilities**|Missing HSTS / BREACH risk|Potential for Man-in-the-Middle (MITM) attacks.|

---
### Determine the Apache version running on app.inlanefreight.local on the target system. (Format: 0.0.0)
```
curl -I http://app.inlanefreight.local
```
- 2.4.41
### Which CMS is used on app.inlanefreight.local on the target system? Respond with the name only, e.g., WordPress.
```
whatweb http://app.inlanefreight.local
```
- Joomla
### On which operating system is the dev.inlanefreight.local webserver running in the target system? Respond with the name only, e.g., Debian.
```
curl -I http://dev.inlanefreight.local
```
- Ubuntu