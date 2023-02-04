## Get hash from password protected file
```bash
gpg2john creds.priv > hash.txt`
```

You can use gpg to extract the messages.
## Import key
```bash
gpg --import creds.priv
```

## Decrypt message
```bash
# import key first
gpg -d creds.txt.gpg

# Alternatively
gpg --decrypt --secret-key <secret_key_file> <encrypted_file> -o <output_file>
```