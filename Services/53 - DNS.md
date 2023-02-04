## Find the domain name
```bash
dnsrecon -r <ip>/24 -n <ip>
dnsrecon -r 192.168.206.196/24 -n 192.16.206.196
```

## List hosts
```bash
host -l <domain> <ip>
```

## Zone transfer
```
dig axfr <domain> @<ip>
dig axfr matrimony.off @192.168.206.196
```