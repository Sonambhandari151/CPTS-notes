
# Protected File Transfers — notes & code



As penetration testers, we often handle sensitive data. Encrypt files before transferring when SSH/SFTP/HTTPS aren't available. Use strong unique passwords per engagement and avoid exfiltrating real PII unless specifically requested by the client.

---

## Invoke-AESEncryption.ps1 (Windows PowerShell)

**Description:** Small PowerShell script to encrypt/decrypt strings and files using AES-CBC with a SHA-256-derived key. Example usages and full script follow.

### Examples

```powershell
# Encrypt a string -> returns Base64 ciphertext
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text"

# Decrypt a Base64-encoded string
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="

# Encrypt a file -> outputs file.bin.aes
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin

# Decrypt a file -> outputs original file
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes
```

### Full script

```powershell
#>
# .SYNOPSIS
#   Small AES helper for encrypting/decrypting files and strings.
# .DESCRIPTION
#   Simple script used in examples for Protected File Transfers.
# .EXAMPLE
#   Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text"
# .EXAMPLE
#   Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
# .EXAMPLE
#   Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
# .EXAMPLE
#   Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes
#>
function Invoke-AESEncryption {
    [CmdletBinding()]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory = $true)]
        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,

        [Parameter(Mandatory = $true)]
        [String]$Key,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]
        [String]$Text,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]
        [String]$Path
    )

    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed
        $aesManaged = New-Object System.Security.Cryptography.AesManaged
        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
    }

    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))

        switch ($Mode) {
            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName + ".aes"
                }

                $encryptor = $aesManaged.CreateEncryptor()
                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)
                $encryptedBytes = $aesManaged.IV + $encryptedBytes
                $aesManaged.Dispose()

                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File encrypted to $outPath"
                }
            }

            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }

                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                $aesManaged.Dispose()

                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File decrypted to $outPath"
                }
            }
        }
    }

    End {
        $shaManaged.Dispose()
        $aesManaged.Dispose()
    }
}
```

### Import & usage

```powershell
# Import module/script
PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1

# Encrypt a file
PS C:\htb> Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
File encrypted to C:\htb\scan-results.txt.aes

# Verify files
PS C:\htb> ls
# ... shows Invoke-AESEncryption.ps1, scan-results.txt, scan-results.txt.aes
```

> **Security note:** Use a unique strong password per engagement. Avoid embedding or reusing weak keys like "p4ssw0rd".

---

## File Encryption on Linux (OpenSSL)

**Example:** Encrypt `/etc/passwd` using AES-256 with PBKDF2 and 100000 iterations.

```bash
s0nam@htb[/htb]$ openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
# enter aes-256-cbc encryption password:
# Verifying - enter aes-256-cbc encryption password:

# To decrypt:
s0nam@htb[/htb]$ openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
# enter aes-256-cbc decryption password:
```

