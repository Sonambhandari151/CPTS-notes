
# 📘 **DNS (Domain Name System)**

👉 DNS = **Domain → IP translation system**  
👉 Works like **internet GPS**

### 🎯 Example

```text
www.example.com → 192.0.2.1
```

---

# 🔹 **Why DNS is Needed**

- Humans → remember names
    
- Computers → use IP addresses  
    👉 DNS converts names → numbers
    

---

# ⚙️ **How DNS Works (Detailed Flow)**

|Step|Component|Function|
|---|---|---|
|1|Browser/OS|Checks **local cache**|
|2|DNS Resolver|Receives query (ISP/public DNS)|
|3|Root Server|Directs to correct TLD|
|4|TLD Server|Points to authoritative server|
|5|Authoritative Server|Returns actual IP|
|6|Resolver|Caches and sends IP|
|7|Client|Connects to server|

---

# 🧠 **DNS Flow Summary**

👉 Client → Resolver → Root → TLD → Authoritative → IP

---

# 📂 **Hosts File**

|Feature|Description|
|---|---|
|Type|Local DNS override|
|Location (Linux)|`/etc/hosts`|
|Location (Windows)|`C:\Windows\System32\drivers\etc\hosts`|
|Format|`<IP> <domain>`|
|Effect|Immediate (no restart needed)|

### 🔧 Uses

- Development testing
    
- Domain blocking
    
- Manual mapping
    

---

# 🔹 **DNS Key Components**

|Component|Role|Example|
|---|---|---|
|Domain Name|Human-readable name|google.com|
|IP Address|Numeric identifier|8.8.8.8|
|DNS Resolver|Query handler|ISP DNS|
|Root Server|Top-level directory|a.root-servers.net|
|TLD Server|Domain extension handler|.com, .org|
|Authoritative Server|Final answer provider|ns1.example.com|

---

# 🔹 **DNS Zone & Zone File**

|Term|Description|
|---|---|
|DNS Zone|Portion of domain managed by admin|
|Zone File|File containing DNS records|
|TTL|Time data is cached|
|SOA|Start of authority record|

---

# 🔥 **Detailed DNS Record Types**

|Record|Full Form|Purpose|Example|Key Point|
|---|---|---|---|---|
|A|Address|Maps domain → IPv4|example.com → 192.0.2.1|Most common|
|AAAA|IPv6 Address|Maps domain → IPv6|→ 2001:db8::1|IPv6 support|
|CNAME|Canonical Name|Alias → another domain|www → example.com|No IP stored|
|MX|Mail Exchange|Email server routing|mail.example.com|Has priority|
|NS|Name Server|Specifies DNS servers|ns1.example.com|Delegation|
|TXT|Text|Stores text data|SPF, verification|Security use|
|SOA|Start of Authority|Zone info|admin, serial, timers|Mandatory record|
|SRV|Service|Service + port mapping|SIP server|Used in VoIP|
|PTR|Pointer|Reverse DNS (IP → domain)|1.2.0.192.in-addr.arpa|Used in logging|

---

# 🔹 **Important Record Fields**

|Field|Meaning|
|---|---|
|TTL|Cache time|
|Priority (MX)|Lower = higher priority|
|Serial (SOA)|Version of zone|
|Refresh|Update interval|
|Retry|Retry time|
|Expire|Zone validity|

---

# 🔹 **"IN" in DNS**

👉 **IN = Internet class** (standard DNS usage)

---

# 🔁 **DNS as Relay Race**

- Request passes through multiple servers
    
- Each server brings it closer to answer
    
- Final IP returned back to user
    

---

# 🔐 **DNS in Web Recon (Very Important)**

| Use Case              | Description                     | Example             |
| --------------------- | ------------------------------- | ------------------- |
| Asset Discovery       | Find subdomains                 | dev.example.com     |
| Infra Mapping         | Identify servers/load balancers | ns, A records       |
| Vulnerability Finding | Outdated servers                | old.dev.example.com |
| Monitoring Changes    | Detect new entries              | vpn.example.com     |
| Tech Detection        | Tools via TXT records           | SPF, 1Password      |

---

# ⚠️ **Advantages & Limitations**

| Type      | Advantage              | Limitation        |
| --------- | ---------------------- | ----------------- |
| DNS Recon | Rich info, easy access | May be incomplete |
| Passive   | Stealthy               | Limited data      |
| Active    | Detailed               | Detectable        |

---

He
# 📘 **DNS Recon Tools (Detailed Table)**

| Tool             | Type  | Key Features                                     | Output Style | Use Cases                          | Advantage         | Limitation       |
| ---------------- | ----- | ------------------------------------------------ | ------------ | ---------------------------------- | ----------------- | ---------------- |
| **dig**          | CLI   | Supports all record types, custom queries, trace | Detailed     | Deep DNS analysis, troubleshooting | Most powerful     | Complex output   |
| **nslookup**     | CLI   | Basic queries (A, MX, AAAA)                      | Simple       | Quick checks                       | Easy to use       | Limited features |
| **host**         | CLI   | Fast lookup, minimal output                      | Very short   | Quick validation                   | Fast & clean      | Less flexible    |
| **dnsenum**      | Tool  | Brute force, zone transfer                       | Automated    | Subdomain discovery                | Saves time        | Detectable       |
| **fierce**       | Tool  | Recursive search, wildcard detection             | Structured   | Subdomain enumeration              | Beginner-friendly | Less detailed    |
| **dnsrecon**     | Tool  | Multiple techniques, export formats              | Detailed     | Full DNS enumeration               | Comprehensive     | Slower           |
| **theHarvester** | OSINT | Emails, names, DNS data                          | Mixed        | Info gathering                     | Multi-source data | Not DNS-only     |
| **Online Tools** | Web   | GUI-based lookup                                 | Simple       | Quick lookup                       | No setup needed   | Limited depth    |

---

# 🔥 **DIG (Domain Information Groper)**

👉 Most important tool for DNS in **CTF + exams**

---

# ⚙️ **Common `dig` Commands (Detailed)**

| Command                         | What it Does            | When to Use         |
| ------------------------------- | ----------------------- | ------------------- |
| `dig domain.com`                | Default A record lookup | Basic resolution    |
| `dig domain.com A`              | IPv4 address            | Find server IP      |
| `dig domain.com AAAA`           | IPv6 address            | IPv6 systems        |
| `dig domain.com MX`             | Mail servers            | Email recon         |
| `dig domain.com NS`             | Name servers            | Infra mapping       |
| `dig domain.com TXT`            | Text records            | SPF, verification   |
| `dig domain.com CNAME`          | Alias mapping           | Subdomain tracking  |
| `dig domain.com SOA`            | Zone authority          | Admin info          |
| `dig @1.1.1.1 domain.com`       | Query specific DNS      | Bypass local DNS    |
| `dig +trace domain.com`         | Full resolution path    | Learn DNS flow      |
| `dig -x IP`                     | Reverse DNS             | IP → domain         |
| `dig +short domain.com`         | Only answer             | Quick result        |
| `dig +noall +answer domain.com` | Clean output            | Filtering           |
| `dig domain.com ANY`            | All records             | Recon (limited use) |

---

# 🧠 **Understanding DIG Output (Important)**

### 📌 1. Header Section

```text
opcode: QUERY, status: NOERROR
```

- **opcode** → type of request
    
- **status** → success/failure
    

```text
flags: qr rd ad
```

- **qr** → response
    
- **rd** → recursion requested
    
- **ad** → authenticated data
    

---

### 📌 2. Question Section

```text
google.com IN A
```

👉 Asking: _What is IPv4 of google.com?_

---

### 📌 3. Answer Section

```text
google.com 0 IN A 142.251.47.142
```

| Part           | Meaning          |
| -------------- | ---------------- |
| google.com     | Domain           |
| 0              | TTL (cache time) |
| IN             | Internet class   |
| A              | Record type      |
| 142.251.47.142 | IP address       |

---

### 📌 4. Footer Section

| Field      | Meaning         |
| ---------- | --------------- |
| Query time | Time taken      |
| SERVER     | DNS server used |
| WHEN       | Timestamp       |
| MSG SIZE   | Response size   |

---

# 🔁 **Important Concepts in DIG**

| Concept              | Meaning                   |
| -------------------- | ------------------------- |
| TTL                  | Cache duration            |
| Recursion            | DNS server resolves fully |
| Authoritative Answer | Final trusted answer      |
| Non-authoritative    | Cached answer             |
| EDNS                 | Extended DNS features     |

---

# ⚡ **Shortcut Commands (Very Useful)**

```bash
dig +short domain.com
```

👉 Only IP (fast output)

```bash
dig +trace domain.com
```

👉 Shows full DNS path

```bash
dig -x IP
```

👉 Reverse lookup

---

# 🔐 **Use in Recon (Very Important)**

| Goal            | Command Example      | Purpose         |
| --------------- | -------------------- | --------------- |
| Find IP         | `dig A domain.com`   | Server location |
| Mail servers    | `dig MX domain.com`  | Email infra     |
| Subdomain clues | `dig NS domain.com`  | DNS servers     |
| Tech detection  | `dig TXT domain.com` | SPF, tools      |
| Reverse lookup  | `dig -x IP`          | Identify host   |

---

# ⚠️ **Important Note (Exam + Real World)**

- DNS queries can be **logged**
    
- Too many queries → **blocked**
    
- Always use with **permission**
    

---

### Which IP address maps to inlanefreight.com?
dig +short inlanefreight.com

### Which domain is returned when querying the PTR record for 134.209.24.248?
dig -x 134.209.24.248 +short

### What is the full domain returned when you query the mail records for facebook.com?
dig facebook.com MX +short