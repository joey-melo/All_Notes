## [[21 - FTP]]
### Unauthenticated
- [ ] Check anonymous access
- [ ] Check software version
### Authenticated
- [ ] Enable/disable passive mode
- [ ] Ensure binary transfer is on for binary files
### Info
- [ ] Usual home directory is `/srv/ftp`

## [[445 - SMB]]
### Unauthenticated
- [ ] List shares
- [ ] Check version
### Authenticated
- [ ] Check for vulnerable software in the share

## 80/443 - HTTP
### General checks
- [ ] Server running
- [ ] Session cookies
### Exploits
- [ ] [SQL/NOSQL injections](SQL)
- [ ] Directory traversal 
- [ ] [XSS](XSS)
- [ ] SSTI 
### Files and Directories
- [ ] robots.txt
- [ ] /cgi-bin/
- [ ] [[Gobuster]]
### Subdomains
- [ ] [[Gobuster]]
- [ ] [[Fuff]]
### Tools
- [ ] [[Nikto]]
- [ ] [[Davtest]]