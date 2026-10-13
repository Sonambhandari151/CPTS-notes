
Subdomain brute-forcing is an **active** discovery technique used to map out a target's attack surface. Unlike passive discovery (which relies on existing logs or third-party data), this method directly queries DNS servers using a predefined list of potential subdomain names.

---

### 1. The Four-Step Enumeration Process

- **Step 1: Wordlist Selection**
    
    The quality of your results depends entirely on your wordlist.
    
    - **General-Purpose:** Broad lists containing common terms like `dev`, `test`, `api`, or `mail`. Best for initial "blind" discovery.
        
    - **Targeted:** Lists tailored to the target’s specific industry (e.g., using "banking" or "fintech" terms) or observed naming conventions.
        
    - **Custom:** Created using intelligence gathered from earlier reconnaissance phases (e.g., using keywords found on the target's main website).
        
- **Step 2: Iteration and Querying**
    
    An automated tool takes each entry from the wordlist and prepends it to the root domain.
    
    - _Example:_ If the word is `billing` and the domain is `example.com`, the tool constructs `billing.example.com`.
        
- **Step 3: DNS Lookup**
    
    The tool performs a DNS query (usually for **A** or **AAAA** records) for the constructed name. If the DNS server returns an IP address, the subdomain is considered "live."
    
- **Step 4: Filtering and Validation**
    
    Valid results are logged. Advanced workflows involve secondary validation, such as checking for HTTP response codes (200 OK vs. 404 Not Found) to ensure the subdomain hosts an active service.
    

---

### 2. Industry Standard Tools

|**Tool**|**Key Characteristics**|
|---|---|
|**dnsenum**|A comprehensive Perl-based toolkit for DNS recon and brute-forcing.|
|**fierce**|Known for recursive discovery and identifying "wildcard" DNS configurations.|
|**dnsrecon**|A versatile tool that mixes brute-force with other techniques like SRV record enumeration.|
|**amass**|The "heavy hitter"; integrates multiple data sources and active/passive techniques.|
|**assetfinder**|A lightweight, fast tool often used in automated bug bounty pipelines.|
|**puredns**|Highly optimized for speed and accuracy when dealing with massive wordlists.|

---

### 3. Deep Dive: `dnsenum`

`dnsenum` is a powerful, multi-functional tool used for more than just brute-forcing. Its capabilities include:

- **DNS Record Harvesting:** Automatically pulls A, MX, NS, and TXT records.
    
- **Zone Transfers (AXFR):** Attempts to copy the entire DNS zone file from a name server (rarely successful on modern systems but devastating if it works).
    
- **Google Scraping:** Uses search engine results to find subdomains not found in standard wordlists.
    
- **Reverse Lookups:** Identifies other domains sharing the same IP space.
    
- **WHOIS Queries:** Gathers ownership and registration data.
    

#### Practical Implementation

To run a thorough scan against a target like `inlanefreight.com`, the following command structure is used:

Bash

```
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
```

**Command Breakdown:**

- `--enum`: A shortcut flag that runs multiple functions (record enumeration, zone transfers, etc.) in one go.
    
- `-f [path]`: Points the tool to a specific wordlist (in this case, a common list from the **SecLists** repository).
    
- `-r`: Enables **recursive** scanning. If `dev.example.com` is found, the tool will then try to find `test.dev.example.com`.
    

---

### 4. Key Takeaways for Success

> **Warning:** Since this is an **active** technique, your IP address will interact directly with the target's DNS infrastructure. This can be logged by security monitoring systems (IDS/IPS).
> 
> - **Use High-Quality Wordlists:** Start with SecLists for reliability.
>     
> - **Handle Wildcards:** Be aware that some domains use wildcard DNS (where _any_ subdomain resolves to an IP), which can lead to thousands of false positives.
>     
> - **Recursion is Key:** Often, the most vulnerable assets are nested deep within the infrastructure (e.g., `internal.staging.api.example.com`).



---
### Using the known subdomains for inlanefreight.com (www, ns1, ns2, ns3, blog, support, customer), find any missing subdomains by brute-forcing possible domain names. Provide your answer with the complete subdomain, e.g., www.inlanefreight.com.
```
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```
- my.inlanefreight.com

