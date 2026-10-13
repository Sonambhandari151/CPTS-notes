## .well-known URIs: Standardized Metadata Discovery

Defined in **RFC 8615**, the `.well-known` directory is a standardized location on a web server used for discovering critical metadata, protocols, and security policies. It provides a consistent path (`/.well-known/`) so that browsers and security tools can automatically find configuration files without manual searching.

---

### 1. How .well-known Works

Instead of placing configuration files in random directories, the `.well-known` standard centralizes them. A client (like a security scanner or an email server) constructs a URL by appending a specific suffix to the `/.well-known/` path.

- **Example:** To find a site's vulnerability reporting policy, a tool would automatically check: `https://example.com/.well-known/security.txt`.
    

---

### 2. Common .well-known URIs

The **Internet Assigned Numbers Authority (IANA)** maintains a registry of these suffixes. Below are high-value examples for reconnaissance:

|**URI Suffix**|**Description**|**Use Case**|
|---|---|---|
|**`security.txt`**|Contact info for reporting security bugs.|Identifying the organization's security team.|
|**`openid-configuration`**|Metadata for OpenID Connect (OAuth 2.0).|Mapping authentication endpoints and keys.|
|**`assetlinks.json`**|Verifies ownership of mobile apps.|Linking a domain to its official Android/iOS apps.|
|**`change-password`**|Standardized redirect to password reset.|Identifying user-account management pages.|
|**`mta-sts.txt`**|Email security policy (MTA-STS).|Assessing email encryption and transport security.|

---

### 3. Deep Dive: `openid-configuration`

In web reconnaissance, this is one of the most powerful discovery points. It reveals the entire architecture of a site's Identity Provider (IdP).

#### Extracted Data points:

- **Authorization Endpoint:** Where users go to log in.
    
- **Token Endpoint:** Where the application exchanges codes for access tokens.
    
- **JWKS URI:** Points to the public keys used to sign security tokens. Analyzing these can reveal the cryptographic strength of the system.
    
- **Scopes Supported:** Shows what data the server can share (e.g., `email`, `profile`, `groups`).
    

---

### 4. Why .well-known is Vital for Reconnaissance

For a security professional, these URIs act as "pre-defined" endpoints that bypass the need for intensive directory brute-forcing.

- **Endpoint Discovery:** Quickly identifies login portals, API endpoints, and certificate locations.
    
- **Protocol Analysis:** Reveals what versions of protocols are running (e.g., OAuth 2.0 vs. OpenID Connect) and what algorithms they support (e.g., RS256).
    
- **Policy Assessment:** `security.txt` and `mta-sts.txt` reveal how mature an organization's security policies are and provide direct lines of communication for responsible disclosure.
    

---

### 5. Reconnaissance Strategy

When auditing a target, checking the `/.well-known/` directory should be an early, automated step:

1. **Check `security.txt`** to understand the scope of their bug bounty or disclosure program.
    
2. **Check `openid-configuration`** to map out the authentication landscape.
    
3. **Check `assetlinks.json`** to expand the attack surface to the target's mobile applications.
    

