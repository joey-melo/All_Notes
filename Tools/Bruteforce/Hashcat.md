```bash
hashcat [-m <mode>] <hash> <wordlist>
```

## Salted Hashes
Let's say you know the formula for the hash is ``md5($s.$p)`` where $s is a defined salt and $p is the password:
1. set the hash file to `hash:knownsalt`. e.g. `13edad4932da9dbb57d9cd15b66ed104:NaCl`
2. use mode 20 (for this example).
```bash
hashcat -m 20 hash.txt /usr/share/wordlists/rockyou.txt
```