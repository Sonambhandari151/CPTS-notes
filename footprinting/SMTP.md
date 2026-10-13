- PRotocol foe sending emails in an IP network.
- Used between an email client and outgoing mail server or between two SMTP servers.
- Combined with IMAP and POP3 protocols.
- By default, port 25.
- Newer SMTP use TCP port 587.
### 1. Core Functionality

- **Purpose:** The primary protocol used to **send** email from a client to a server, or between servers.
    
- **Transmission Process:**
    
    1. **Handshake:** Client confirms identity via username/password.
        
    2. **Data Transfer:** Client sends sender/recipient addresses, email content, and metadata.
        
    3. **Termination:** Connection closes after the email is fully transmitted.
        
    4. **Relay:** The server then acts as a "relay," sending the mail to the next SMTP server in the chain until it reaches the destination.
        

---

### 2. Encryption & Security (The Transition from Plaintext)

By default, standard SMTP is **insecure** because it transmits data in plaintext. To secure it, we use **SSL/TLS**.

#### **STARTTLS (Opportunistic TLS)**

- Starts as a plaintext connection on a standard port (usually 587).
    
- The client issues the `STARTTLS` command.
    
- The connection "upgrades" to an encrypted state **before** authentication data (passwords) is sent.
    

#### **Implicit TLS**

- The connection is encrypted from the very first second (the "handshake" is encrypted).
    
- Usually associated with Port 465.
---
## The SMTP Delivery Ecosystem

The journey of an email is a hand-off between several specialized software components, collectively moving the message from the sender's screen to the recipient's inbox.

### 1. The Key Actors (The "Agents")

| **Agent** | **Full Name**             | **Role**                                                                                                                                      |
| --------- | ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **MUA**   | **Mail User Agent**       | The client software (e.g., Outlook, Gmail, Thunderbird) where the user writes and sends the email.                                            |
| **MSA**   | **Mail Submission Agent** | The first point of contact. It receives the email from the MUA and checks its validity (origin/authentication) to reduce the load on the MTA. |
| **MTA**   | **Mail Transfer Agent**   | The "post office" of the system. It handles the actual routing, DNS lookups for the recipient's IP, and the transfer of mail between servers. |
| **MDA**   | **Mail Delivery Agent**   | Once the email arrives at the destination server, the MDA takes it from the MTA and places it into the correct user's local mailbox.          |

---

### 2. The Step-by-Step Flow

1. **Preparation:** The **MUA** converts your message into a standardized format consisting of a **Header** (metadata) and a **Body** (content).
    
2. **Submission:** The MUA uploads the email to the **MSA** using **SMTP-Auth** (an ESMTP extension) to prove the sender is authorized.
    
3. **Relay:** The **MTA** takes the email, queries **DNS** for the recipient's Mail Exchange (MX) records, and forwards the data packets to the destination MTA.
    
4. **Delivery:** The destination **MDA** reassembles the packets and deposits them into the recipient's mailbox.
    
5. **Retrieval:** The recipient uses protocols like **POP3** or **IMAP** to pull the email from the mailbox into their own MUA.
    

---
**SMTP – Disadvantages & Security Improvements **

---

### 1. Disadvantage: No Proper Delivery Confirmation

- SMTP does **not guarantee clear delivery status**.
    
- Even though notifications exist, **format is not standardized**.
    
- Result:
    
    - Users usually receive **raw error messages (in English)**.
        
    - Often includes **email headers**, which are hard to understand.
        
- No reliable **user-friendly delivery receipt system**.
    

---

### 2. Disadvantage: No Built-in Authentication

- SMTP **does not verify sender identity** during connection.
    
- Anyone can send mail using **fake sender address**.
    
- Leads to:
    
    - **Email spoofing**
        
    - **Spam attacks**
        
- Open SMTP relays:
    
    - Misused to send **bulk spam emails**
        
    - Attackers stay **untraceable**
        

---

### 3. Security Solutions to SMTP Issues

Modern systems use multiple techniques:

#### a. Spam Filtering

- Suspicious emails:
    
    - **Rejected** OR
        
    - Sent to **Spam/Quarantine folder**
        

#### b. Authentication Mechanisms

- **SPF (Sender Policy Framework)**
    
    - Verifies if sending server is authorized
        
- **DKIM (DomainKeys Identified Mail)**
    
    - Uses **digital signatures** to verify sender integrity
        

---

### 4. Extended SMTP (ESMTP)

- Improved version of SMTP
    
- Adds **security + functionality**
    
- When people say SMTP → usually mean **ESMTP**
    

---

### 5. Encryption using TLS

- Process:
    
    1. Client sends **EHLO**
        
    2. Server responds
        
    3. Client sends **STARTTLS**
        
- Result:
    
    - Connection upgraded to **secure (SSL/TLS)**
        
    - Entire communication becomes **encrypted**
        

---

### 6. Secure Authentication (AUTH)

- After encryption:
    
    - **AUTH PLAIN** can be used safely
        
- Provides:
    
    - **User authentication**
        
    - Prevents unauthorized access
        

---
# SMTP 

## 1. Default Configuration (Postfix)

- Config file: `/etc/postfix/main.cf`
    
- Important parameters:
    

|Setting|Meaning|
|---|---|
|`myhostname`|Mail server hostname|
|`mydestination`|Domains handled locally|
|`mynetworks`|Trusted IP ranges|
|`inet_protocols`|IPv4/IPv6 support|
|`home_mailbox`|Mail storage path|
|`smtp_bind_address`|IP used for outgoing mail|

### Key Point

- SMTP server **only sends & forwards emails**, not responsible for retrieval.
    

---

## 2. SMTP Commands (Very Important)

|Command|Function|
|---|---|
|`HELO / EHLO`|Start session (EHLO = extended SMTP)|
|`AUTH PLAIN`|Authentication|
|`MAIL FROM`|Sender address|
|`RCPT TO`|Recipient address|
|`DATA`|Start email body|
|`RSET`|Reset session|
|`VRFY`|Verify user existence|
|`EXPN`|Expand mailing list|
|`NOOP`|Keep connection alive|
|`QUIT`|End session|

---

## 3. SMTP Connection via Telnet

### Steps:

1. Connect:
    
    ```
    telnet <IP> 25
    ```
    
2. Start session:
    
    ```
    HELO domain.com
    ```
    
    or
    
    ```
    EHLO domain.com
    ```
    

### EHLO Output

Shows server capabilities:

- PIPELINING
    
- SIZE
    
- SMTPUTF8
    
- DSN
    
- CHUNKING
    

---

## 4. User Enumeration (VRFY)

```
VRFY username
```

### Responses:

- `250` → User exists
    
- `252` → Cannot verify (may be fake positive)
    

### Important:

- **VRFY is unreliable**
    
- Servers may return valid responses for non-existing users
    

---

## 5. Sending Email via Command Line

### Steps:

```
EHLO domain
MAIL FROM:<sender@mail.com>
RCPT TO:<receiver@mail.com>
DATA
```

### Email Format:

```
From:
To:
Subject:
Date:

Message body
.
```

`.` → ends message

---

## 6. Email Header (RFC 5322)

Contains:

- Sender & receiver
    
- Timestamp
    
- Mail servers passed (trace)
    
- Content type
    

### Important:

- Visible to both sender & receiver
    
- Useful in **forensics & phishing analysis**
    

---

## 7. Dangerous Configuration (Open Relay)

### Misconfiguration:

```
mynetworks = 0.0.0.0/0
```

### Meaning:

- Allows **any IP to send emails**
    

### Risks:

- Spam relay
    
- Email spoofing
    
- Phishing attacks
    
- Reputation damage
    

---

## 8. Open Relay Attack

Attacker can:

- Send fake emails
    
- Impersonate users
    
- Relay emails between external domains
    

---

## 9. Footprinting SMTP

### Nmap Scan:

```
nmap -sC -sV -p25 <IP>
```

### Output reveals:

- SMTP version
    
- Supported commands (via `smtp-commands`)
    

---

## 10. Detect Open Relay (Nmap Script)

```
nmap -p25 --script smtp-open-relay <IP>
```

### Output:

- Runs multiple tests (16 cases)
    
- If vulnerable → “Server is an open relay”
    

---

## 11. Web Proxy SMTP Access

If behind proxy:

```
CONNECT <IP>:25 HTTP/1.0
```

---

### Q1 Enumerate the SMTP service and submit the banner, including its version as the answer.
- telnet 10.129.46.240 25

### Q2 Enumerate the SMTP service even further and find the username that exists on the system. Submit it as the answer.
- smtp-user-enum -M VRFY -U footprinting-wordlist.txt -t 10.129.46.240 -v -w20

