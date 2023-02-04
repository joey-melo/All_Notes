## Subdomain Bruteforce
```bash
ffuf -u 'https://broscience.htb' -H 'Host: FUZZ.broscience.htb' -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -fs 9308 -o subdomains.json
```

## HTTP Post Form Bruteforce
