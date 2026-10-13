### DNS Zone Transfer (AXFR) — Quick Notes

**Definition**

- DNS Zone Transfer = copy of all DNS records (domain + subdomains) from primary → secondary DNS server.
    

---
![[Pasted image 20260409180915.png]]
### Working Steps

1. **AXFR Request** → Secondary server requests full zone
    
2. **SOA Record** → Primary sends zone info (serial, etc.)
    
3. **Records Transfer** → A, AAAA, MX, NS, TXT, etc.
    
4. **Completion** → Transfer ends
    
5. **ACK** → Confirmation from secondary
    

---

### Why Important (Security)

- If misconfigured → **anyone can request zone transfer**
    
- Leads to **information disclosure**
    

---

### What Attacker Gets

- All **subdomains** (hidden ones too)
    
- **IP addresses**
    
- **Name servers**
    
- Internal infrastructure mapping
    

---

### Exploitation Command

```bash
dig axfr @<nameserver> <domain>
```

**Example:**

```bash
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

---

### Key Indicator

- If AXFR works → **server is vulnerable**
    
- If fails → properly secured
    

---

### Prevention

- Allow zone transfer **only to trusted servers**
    
- Restrict via ACLs (Access Control Lists)
    

---
### After performing a zone transfer for the domain inlanefreight.htb on the target system, how many DNS records are retrieved from the target system's name server? Provide your answer as an integer, e.g, 123.

```
dig axfr @10.129.60.206 inlanefreight.htb
```
- 22

### Within the zone record transferred above, find the ip address for ftp.admin.inlanefreight.htb. Respond only with the IP address, eg 127.0.0.1
- 10.10.34.2

### Within the same zone record, identify the largest IP address allocated within the 10.10.200 IP range. Respond with the full IP address, eg 10.10.200.1
- 10.10.200.14