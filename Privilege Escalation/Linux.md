## Useful Scripts

```bash
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt # List SUIDs
find / -user $USER 2>/dev/null | grep -v "proc\|sys\|run" > $USER-files.txt # List user files
find / -group $GROUP -ls 2>/dev/null | grep -v "home\|tmp\|proc" > group-files.txt # List group files
find /etc -type f -writable 2>/dev/null # List writable files
```

## Alternative Commands

```bash
ifconfig; ip a s; ip a
netstat; ss
```

## Notable Directories

```bash
/opt # non-standard applications installed
/etc/apache2/sites-enabled # apache2 configs
/var/www # website root
/var/log # log files
/var/backups # backup files
```

## Tools
Pspy:
```bash
./pspy
./pspy -r /opt # watch only /opt folder
```

## Docker Escape

### References

- Sirol - Proving Grounds
- Deployer - Proving Grounds
- Shoppy - Hackthebox

## Code Compilation
```bash
mingw32 # Compile Windows code
```