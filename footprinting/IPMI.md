
## 🖥️ IPMI: Intelligent Platform Management Interface

### 1. Core Definition

- **Purpose:** A set of standardized specifications for **hardware-based** host management.
    
- **Autonomy:** Operates as an independent subsystem. It works regardless of the state of the CPU, BIOS, or Operating System (OS).
    
- **Connectivity:** Uses a direct network connection to the hardware, requiring only a power source and a LAN connection to function.
    

---

### 2. Primary Use Cases

IPMI is essential for managing "headless" servers or recovering from critical failures:

- **Pre-Boot:** Modifying BIOS/UEFI settings before the OS starts.
    
- **Powered Down:** Managing or powering on a host that is currently turned off.
    
- **System Failure:** Accessing a host remotely after the OS has crashed or become unresponsive.
    
- **Remote Upgrades:** Performing system updates without needing physical access to the server room.
    

---

### 3. Monitoring & Maintenance Functions

When not being used for emergency recovery, IPMI acts as a hardware watchdog:

- **Sensor Monitoring:** Tracks system temperature, voltage, fan speeds, and power supply health.
    
- **Inventory & Logs:** Queries hardware inventory and reviews system event logs.
    
- **Alerting:** Uses **SNMP** (Simple Network Management Protocol) to send alerts when hardware thresholds are exceeded.
    
- **Serial over LAN (SoL):** IPMI 2.0 allows administrators to view the serial console output remotely as if they were plugged into the physical port.
    

---

### 4. Essential Hardware Components

To function, IPMI relies on a specific ecosystem of internal components:

| **Component**        | **Full Name**                           | **Role**                                                                                                                        |
| -------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **BMC**              | **Baseboard Management Controller**     | The "brain" of the IPMI; a microcontroller that manages the interface between system management software and platform hardware. |
| **ICMB**             | **Intelligent Chassis Management Bus**  | An interface that allows communication between different server chassis.                                                        |
| **IPMB**             | **Intelligent Platform Management Bus** | An internal bus that extends the BMC to other components within the chassis.                                                    |
| **IPMI Memory**      | Storage                                 | stores things such as the system event log, repository store data, and more                                                     |
| **Comm. Interfaces** | Interfaces                              | Includes local system interfaces, serial/LAN ports, and the PCI Management Bus.                                                 |



---

## 🔍 Footprinting IPMI Services

### 1. The Target: Port 623 UDP

IPMI always communicates over **UDP port 623**. When you see this port open, you aren't just talking to an operating system; you are talking to the **Baseboard Management Controller (BMC)**—an embedded ARM system (usually running Linux) attached to the motherboard.

### 2. Common Vendor Implementations

Different manufacturers have their own "brand names" for their BMC/IPMI implementations. You will most likely encounter these three:

- **HP iLO** (Integrated Lights-Out)
    
- **Dell iDRAC** (Integrated Dell Remote Access Controller)
    
- **Supermicro IPMI**
    

---

## 🛠️ Scanning Techniques

### A. Using Nmap

Nmap includes a specific script (`ipmi-version`) to pull metadata from the service.

- **Command:** `sudo nmap -sU --script ipmi-version -p 623 <Target_IP>`
    
- **What to look for:** * **Version:** IPMI-2.0 is the most common and contains a known security flaw.
    
    - **UserAuth:** Shows if the "null user" or specific authentication levels are enabled.
        

### B. Using Metasploit

Metasploit’s discovery module is faster for scanning large networks (CIDR ranges).

- **Module:** `auxiliary/scanner/ipmi/ipmi_version`
    
- **Benefit:** It clearly lists the supported hashing algorithms (like MD5 or SHA1) used for passwords, which helps in planning a brute-force attack.
    

---

## 🔑 The "Default Password" Cheatsheet

Because these are hardware-level tools, they are often overlooked during standard software updates. Always try these defaults first:

|**Vendor**|**Username**|**Password**|
|---|---|---|
|**Dell iDRAC**|`root`|`calvin`|
|**Supermicro**|`ADMIN`|`ADMIN`|
|**HP iLO**|`Administrator`|_(Random 8-char string found on a physical sticker)_|

---

## ⚠️ Why IPMI Access = Full Compromise

Gaining access to a BMC is **equivalent to having physical access** to the server. If you get into the web console or SSH via these credentials, you can:

1. **Monitor:** View the screen in real-time (KVM).
    
2. **Control:** Power the server off or reboot it.
    
3. **Infect:** Mount a virtual ISO/DVD to reinstall the OS with your own malicious version.
    
4. **Steal:** Access the host's motherboard logs and hardware info.
    

---

## 🔓 The IPMI 2.0 Hash Retrieval Flaw

### 1. The Vulnerability (The "RAKP" Problem)

The **Remote Authenticated Key Exchange Protocol (RAKP)** is the handshake used by IPMI 2.0.

- **The Flaw:** As part of the standard connection process, the server sends the client a **salted SHA1 or MD5 hash** of the user's password _before_ the client has even proven they know the password.
    
- **The Impact:** An attacker can request a connection for **any valid username** (like `ADMIN`, `root`, or `Administrator`), and the server is forced to provide the hash.
    

> **Important Note:** This is not a "bug" that can be patched with a software update. It is a fundamental part of how the IPMI 2.0 specification was designed.

---

## 🛠️ Exploitation Workflow

### A. Dumping Hashes with Metasploit

The most common tool for this is the Metasploit module `auxiliary/scanner/ipmi/ipmi_dumphashes`.

- **Process:** The module sends a "Sign-on" request to port 623/UDP.
    
- **Output:** It returns a long string containing the salt and the hash.
    
- **Automation:** Metasploit can automatically try to crack common passwords (like `ADMIN` or `calvin`) as soon as it grabs the hash.
    

### B. Offline Cracking with Hashcat

If the password isn't a simple default, you must take the hash string and crack it offline.

- **Hashcat Mode:** **7300** (IPMI2 RAKP HMAC-SHA1).
    
- **The HP iLO Special:** HP iLO systems often have an 8-character randomized password (numbers + uppercase). You can target this specifically with a **Mask Attack**:
    
    - `hashcat -m 7300 hash.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u`
        
    - _(This tells Hashcat to try every combination of Digits (?d) and Uppercase (?u) for 8 positions.)_
        

---

## 🚩 The "Big Picture" Risk

Why do penetration testers love finding IPMI?

1. **Lateral Movement:** Administrators often reuse the IPMI password for other critical systems (SSH, Web Portals, Domain Accounts).
    
2. **Full Control:** Once you have the password, you can use the web console to see the server's screen, mount a malicious ISO, or simply turn the power off.
    
3. **Persistence:** Because BMCs are independent of the OS, an attacker can maintain access even if the main server is wiped and reinstalled.
    

---

## 🛡️ Mitigation Strategies

Since the protocol itself is flawed, you cannot "fix" it. Instead, you must **defend** it:

- **Network Segmentation:** Place all BMCs/IPMI ports on a dedicated "Management VLAN" that is physically or logically separated from the rest of the network.
    
- **Strong Passwords:** Use long, complex passwords (20+ characters) to make offline cracking mathematically impossible.
    
- **Access Control:** Use firewalls/ACLs to ensure only specific administrator IP addresses can talk to port 623.
    

---
### What username is configured for accessing the host via IPMI?
```
sudo nmap -sU -p 623 --script ipmi-version 10.129.4.23
msfconsole
use auxiliary/scanner/ipmi/ipmi_dumphashes
set RHOSTS 10.129.4.23
run
```
- admin

### What is the account's cleartext password?
```
nano ipmi.txt
cb3b8a9882000000e56eeb3d5540449d10c1866fc4226401a13a75f6a23f3ca2daeb34b50fcdc0e2a123456789abcdefa123456789abcdef140561646d696e:fe65cf8f4ba94844d11d326f82dfaa44e8c987c9
hashcat -m 7300 ipmi.txt /usr/share/wordlists/rockyou.txt

```
- trinity