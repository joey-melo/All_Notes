## Quick scan
```bash
nmap --top-ports 100 <ip>
```

## Full scan
```bash
nmap -sC -sV -p- -oA tcp_scan <ip>
```

## Vuln scan
```bash
nmap --script vuln -oN vuln_scan.nmap -p <port> <ip>
```

## UDP scan
```bash
sudo nmap -sU --top-ports 100 -oN udp_scan.nmap <ip>
```