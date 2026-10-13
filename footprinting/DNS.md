- DNS is a system for resolving computer names into IP addresses, and it does not have a central database.

## Types of DNS Worldwide
- DNS root server
- Authoritative name server
- Non-authoritative name server
- Caching server
- Forwarding server
- Resolver

|Server Type|Description|
|---|---|
|DNS Root Server|Root servers handle top-level domains (TLDs). They are queried when other name servers do not respond. They act as a central interface linking domain names to IP addresses. Managed by ICANN, there are 13 root servers worldwide.|
|Authoritative Nameserver|Holds authority for a specific DNS zone and provides final (binding) answers. If it cannot resolve a query, the request may go to root servers. It helps recursive servers find correct web servers.|
|Non-authoritative Nameserver|Does not manage a DNS zone. It gathers information using recursive or iterative queries and provides cached or indirect answers.|
|Caching DNS Server|Stores DNS query results temporarily. The storage duration is defined by the authoritative server (TTL). Improves performance by reducing repeated queries.|
|Forwarding Server|Forwards DNS queries to another DNS server instead of resolving them itself.|
|Resolver|Performs local name resolution on a computer or router. It is not an authoritative server but helps initiate DNS queries.|

---
- DNS is unencrypted.
- By default, IT security professionals apply `DNS over TLS` (`DoT`) or `DNS over HTTPS` (`DoH`) here.
- In addition, the network protocol `DNSCrypt` also encrypts the traffic between the computer and the name server.
- Not only IP mapping but also stores  & outputs additional info about sevices associated with a domain.
![[Pasted image 20260331174706.png]]

|**DNS Record**|**Description**|
|---|---|
|`A`|Returns an IPv4 address of the requested domain as a result.|
|`AAAA`|Returns an IPv6 address of the requested domain.|
|`MX`|Returns the responsible mail servers as a result.|
|`NS`|Returns the DNS servers (nameservers) of the domain.|
|`TXT`|This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.|
|`CNAME`|This record serves as an alias for another domain name. If you want the domain [www.hackthebox.eu](http://www.hackthebox.eu/) to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for [www.hackthebox.eu](http://www.hackthebox.eu/).|
|`PTR`|The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.|
|`SOA`|Provides information about the corresponding DNS zone and email address of the administrative contact.|
- The `SOA` record is located in a domain's zone file and specifies who is responsible for the operation of the domain and how DNS information for the domain is managed.

---

# DNS Default Configuration – 

## 1. Types of DNS Configuration Files

All DNS servers use **three types of configuration files**:

### 1) Local DNS Configuration Files

- Used to define **server settings and zones**
    
- These files control how the DNS server behaves
    

### 2) Zone Files

- Text files that describe a **DNS zone**
    
- Contain mappings of:
    
    - domain names
        
    - hostnames
        
    - IP addresses
        

### 3) Reverse Name Resolution Files

- Used for **reverse lookup**
    
- Map **IP address → Fully Qualified Domain Name (FQDN)**
    

---

# 2. Bind9 DNS Server Configuration

- **Bind9** is commonly used on Linux systems
    
- Main configuration file: `named.conf`
    

## Structure of `named.conf`

It is divided into **two sections**:

### 1) Options Section

- Contains **general (global) settings**
    

### 2) Zone Section

- Contains **domain-specific configurations**
    

---

# 3. Local Configuration Files

Common files:

- `named.conf.local` → defines zones
    
- `named.conf.options` → global settings
    
- `named.conf.log` → logging
    

These files also include **RFC references**, allowing customization of:

- server behavior
    
- domain structure
    
- zones for different domains
    

---

# 4. Global Options vs Zone Options

### Global Options

- Apply to **all zones**
    
- Defined in configuration files
    

### Zone Options

- Apply only to a **specific zone**
    

### Important Rule:

- If an option exists in both:
    
    - **Zone option takes precedence**
        

### Default Behavior:

- If an option is not defined → **default value is used**
    

---

# 5. Local DNS Configuration (`named.conf.local`)

### Example:

```bash
zone "domain.com" {
    type master;
    file "/etc/bind/db.domain.com";
    allow-update { key rndc-key; };
};
```

### Explanation:

- `zone "domain.com"`  
    → Defines a **DNS zone for the domain**
    
- `type master`  
    → This server is the **authoritative (primary) server** for this zone
    
- `file "/etc/bind/db.domain.com"`  
    → Specifies the **zone file location**
    
- `allow-update { key rndc-key; };`  
    → Allows **dynamic updates** using a secure key
    

---

### Additional Notes:

- Multiple zones can be defined in this file
    
- Each zone usually has **its own file**
    
- Exceptions: ISP and public DNS servers (handle many domains)
    

---

# 6. Zone Files (Forward Lookup)

## Definition

- A **zone file** is a text file in **BIND format**
    
- Represents a **point of delegation in DNS hierarchy**
    
- Describes the **entire zone**
    

---

## Rules:

- Must contain:
    
    - **Exactly one SOA record**
        
    - **At least one NS record**
        
- SOA record is usually placed **at the beginning**
    
- Syntax errors → zone becomes unusable → DNS returns **SERVFAIL**
    

---

## Purpose:

- Stores **forward lookup records**
    
- Helps DNS server identify:
    
    - domain
        
    - hostname
        
    - associated IP address
        
- Works like a **phone book**
    

---

## Example Zone File Explanation

### Basic Settings:

```bash
$ORIGIN domain.com
$TTL 86400
```

- `$ORIGIN` → base domain
    
- `$TTL` → default cache time (in seconds)
    

---

### SOA Record:

```bash
@ IN SOA dns1.domain.com. hostmaster.domain.com. (
 2001062501
 21600
 3600
 604800
 86400 )
```

- Defines **authority for the zone**
    
- Contains:
    
    - primary DNS server
        
    - admin email
        
    - timing parameters
        

---

### NS Records:

```bash
IN NS ns1.domain.com.
IN NS ns2.domain.com.
```

- Define **name servers for the domain**
    

---

### MX Records:

```bash
IN MX 10 mx.domain.com.
IN MX 20 mx2.domain.com.
```

- Define **mail servers**
    
- Priority: lower value = higher priority
    

---

### A Records:

```bash
server1 IN A 10.129.14.5
server2 IN A 10.129.14.7
ns1 IN A 10.129.14.2
ns2 IN A 10.129.14.3
```

- Map **hostname → IP address**
    

---

### CNAME Records:

```bash
ftp IN CNAME server1
mx IN CNAME server1
www IN CNAME server2
```

- Define **aliases**
    
- Point to another hostname
    

---

# 7. Reverse Name Resolution Zone Files

## Purpose:

- Used to resolve **IP address → FQDN**
    

---

## Example:

```bash
$ORIGIN 14.129.10.in-addr.arpa
```

- Represents reverse mapping of:
    
    ```
    10.129.14.x
    ```
    

---

## PTR Records:

```bash
5 IN PTR server1.domain.com.
```

### Explanation:

- Maps:
    
    ```
    10.129.14.5 → server1.domain.com
    ```
    
- Only the **last octet** is written because the rest is defined in `$ORIGIN`
    

---

## Key Role of PTR:

- Responsible for **reverse translation of IP to domain name**
    

---

# 8. Important Behavior

- If a **zone file has syntax errors**:
    
    - It becomes unusable
        
    - DNS behaves as if the zone does not exist
        
    - Responds with:
        
        ```
        SERVFAIL
        ```
        

---

# 9. Final Understanding

- Local config files define **zones and behavior**
    
- Zone files store **forward mappings**
    
- Reverse files store **reverse mappings**
    
- DNS uses these files to resolve:
    
    - domain → IP
        
    - IP → domain
        

---
# 1. Dangerous Settings in DNS

DNS servers can be **attacked in multiple ways**, especially when misconfigured.

- Vulnerabilities exist in **Bind9**
    
- Misconfigurations happen because:
    
    - DNS is complex
        
    - Administrators prioritize **functionality over security**
        
    - Temporary fixes may expose the system
        

This leads to **security weaknesses**

---

# 2. Important Risky Configuration Options

These settings can introduce vulnerabilities if not configured properly:

### 1) `allow-query`

- Defines **which hosts can query the DNS server**
    
- If misconfigured:
    
    - Anyone can query the server
        
    - Leads to **information exposure**
        

---

### 2) `allow-recursion`

- Defines **which hosts can perform recursive queries**
    
- Recursive query:
    
    - DNS server fetches answer from other servers
        
- If open to all:
    
    - Can be abused for:
        
        - **DNS amplification attacks**
            
        - Unauthorized data access
            

---

### 3) `allow-transfer`

- Defines **which hosts can perform zone transfers**
    
- Zone transfer:
    
    - Copy of entire zone database
        
- If unrestricted:
    
    - Attacker can download:
        
        - all DNS records
            
        - internal structure
            

---

### 4) `zone-statistics`

- Collects **statistical data about zones**
    
- Risk:
    
    - May expose internal DNS usage patterns
        

---

# 3. Footprinting DNS Service

## Concept

- Footprinting = **gathering information about DNS server**
    
- Done by sending **queries to DNS server**
    

---

# 4. NS Query (Finding Name Servers)

## Command:

```bash
dig ns inlanefreight.htb @10.129.14.128
```

## Purpose:

- Ask DNS server:
    
    - “Which name servers handle this domain?”
        

---

## Output Explanation:

### Header:

```
status: NOERROR
```

- Query successful
    

---

### Question Section:

```
inlanefreight.htb IN NS
```

- Asking for NS records
    

---

### Answer Section:

```
inlanefreight.htb IN NS ns.inlanefreight.htb.
```

- Shows **authoritative name server**
    

---

### Additional Section:

```
ns.inlanefreight.htb IN A 10.129.34.136
```

- Provides IP of name server
    

---

## Key Idea:

- If multiple DNS servers exist:
    
    - We can query them individually
        
    - They may have **different configurations or data**
        

---

# 5. Version Query (CHAOS Class)

## Command:

```bash
dig CH TXT version.bind 10.129.120.85
```

## Purpose:

- Retrieve **DNS server version**
    

---

## Explanation:

### CH (CHAOS class)

- Special DNS class used for **internal info**
    

### TXT type

- Requests **text information**
    

---

## Output:

```
"9.10.6-P1"
```

- Shows:
    
    - DNS software version
        

---

## Important Note:

- Works only if:
    
    - Entry exists on server
        
- Otherwise:
    
    - No result
        

---

# 6. ANY Query (All Records)

## Command:

```bash
dig any inlanefreight.htb @10.129.14.128
```

## Purpose:

- Retrieve **all available records** from DNS server
    

---

## Important Note:

- Server returns:
    
    - Only what it is willing to disclose
        
    - Not necessarily all records
        

---

## Output Explanation:

### TXT Records:

```
"v=spf1 include:mailgun.org ..."
```

- SPF records (email security policies)
    

```
"MS=ms97310371"
```

- Verification records
    

---

### SOA Record:

```
inlanefreight.htb IN SOA ...
```

- Shows:
    
    - primary DNS server
        
    - admin info
        
    - timing values
        

---

### NS Record:

```
IN NS ns.inlanefreight.htb.
```

- Name server info
    

---

### Additional Section:

```
ns.inlanefreight.htb IN A 10.129.34.136
```

- IP address of name server
    

---

# 7. Key Understanding

- Misconfigured DNS settings → **security risks**
    
- Footprinting is done by:
    
    - querying DNS server
        
- Important queries:
    
    - **NS** → find name servers
        
    - **CH TXT version.bind** → find version
        
    - **ANY** → get available records
        

---

### Zone Transfer 

- **Zone transfer** means:  
    → Copying DNS data (zone file) from one DNS server to another
    

---

### Why is it needed?

- If one DNS server fails → another should still work
    
- So, same data is stored on **multiple servers (backup servers)**
    
- All servers must have **identical records**
    

---

### How it works?

- Data is transferred using:  
    → **TCP port 53**
    
- This process is called:  
    → **AXFR (Full Zone Transfer)**
    

---

### What happens during transfer?

- Entire zone file (all DNS records) is copied
    
- Servers check:  
    → “Do we both have the same data?”
    

---

### Security part

- Servers use a **secret key (`rndc-key`)**
    
- Ensures:  
    → Only trusted servers can exchange data
    


---

# 1. Primary and Secondary DNS Servers

### Primary Name Server

- Stores the **original zone data**
    
- All changes (add/modify/delete records) happen here
    

---

### Secondary Name Server

- Acts as a **backup**
    
- Stores a **copy of zone data**
    

---

### Why multiple servers?

- Increase **reliability**
    
- Provide **load distribution**
    
- Protect primary server from **attacks**
    

---

### Important Note:

- For some **Top-Level Domains (TLDs)**:  
    → At least **2 DNS servers are mandatory**
    

---

# 2. Master and Slave Concept

### Master Server

- Server that **provides zone data**
    

### Slave Server

- Server that **receives zone data from master**
    

---

### Relationship:

- Primary → always **master**
    
- Secondary → can be:
    
    - **slave (receives data)**
        
    - or **master (passes data further)**
        

---

# 3. Zone Synchronization Process

- Slave server periodically checks master
    

### Step:

1. Slave requests **SOA record**
    
2. Checks **serial number**
    

---

### If serial number:

- **Same** → no update needed
    
- **Higher on master** → update required
    

---

### Timing:

- Controlled by **refresh time** (usually ~1 hour)
    

---

# 4. Zone Transfer (AXFR using dig)

## Command:

```bash
dig axfr inlanefreight.htb @10.129.14.128
```

## Purpose:

- Request **full zone transfer**
    
- Copies entire DNS records
    

---

## Output Explanation:

- Shows:
    
    - **SOA record**
        
    - **TXT records**
        
    - **NS record**
        
    - **A records (hostnames + IPs)**
        

Example:

```
app.inlanefreight.htb → 10.129.18.15
internal.inlanefreight.htb → 10.129.1.6
mail1.inlanefreight.htb → 10.129.18.201
```

---

## Key Idea:

- AXFR reveals **complete DNS structure**
    

---

# 5. Misconfiguration Risk

If `allow-transfer` is:

- set to **any**
    
- or open to subnet
    

→ Then:

- **Anyone can request zone transfer**
    

---

### Result:

- Attacker gets:
    
    - all hostnames
        
    - all IP addresses
        
    - internal network structure
        

---

# 6. Internal Zone Transfer

## Command:

```bash
dig axfr internal.inlanefreight.htb @10.129.14.128
```

## Output:

- Reveals internal systems:
    

Examples:

```
dc1.internal → 10.129.34.16
vpn.internal → 10.129.1.6
ws1.internal → 10.129.1.34
```

---

## Key Point:

- Internal DNS data is exposed
    
- Very dangerous for security
    

---

# 7. Subdomain Brute Forcing

## Concept:

- Guess subdomains using a **wordlist**
    

---

## Bash Example:

```bash
for sub in $(cat wordlist); do
 dig $sub.domain.com @DNS_IP
done
```

---

## Output:

- Finds valid subdomains:
    

```
ns.domain.com → IP
mail1.domain.com → IP
app.domain.com → IP
```

---

# 8. DNSenum Tool

## Command:

```bash
dnsenum --dnsserver 10.129.14.128 --enum -f wordlist inlanefreight.htb
```

---

## What it does:

- Automates:
    
    - zone transfer attempts
        
    - subdomain brute force
        
    - DNS enumeration
        

---

## Output:

- Lists discovered subdomains
    
- Shows if zone transfer is allowed or not
    

---
## Q1
### Interact with the target DNS using its IP address and enumerate the FQDN of it for the "inlanefreight.htb" domain.
```
dig ns inlanefreight.htb @10.129.45.29
```
- ns.inlanefreight.htb

### Q2 Identify if its possible to perform a zone transfer and submit the TXT record as the answer. (Format: HTB{...})
```
 dig axfr internal.inlanefreight.htb @10.129.45.29
```
- HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}
### Q3 What is the IPv4 address of the hostname DC1?
```
 dig axfr internal.inlanefreight.htb @10.129.45.29
```
- 10.129.34.16
### Q4 What is the FQDN of the host where the last octet ends with "x.x.x.203"?
```
 dnsenum --dnsserver 10.129.45.29 --enum -p 0 -s 0 -o subdomain.txt  -f /usr/share/wordlists/seclists/Discovery/DNS/fierce-hostlist.txt dev.inlanefreight.htb

```
- win2k.dev.inlanefreight.htb