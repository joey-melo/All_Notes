## Get hash from password protected file
```bash
gpg2john creds.priv > hash.txt`
```

## Import key
```bash
gpg --import creds.priv
```

## Decrypt message
```bash
# import key first
gpg -d creds.txt.gpg
```