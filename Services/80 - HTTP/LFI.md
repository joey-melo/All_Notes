## LFI (Local File Inclusion)

Check [[Default files]] for more info on Web Servers.

### Useful files
Linux:
```
/etc/passwd
/etc/hosts
/etc/crontab
/etc/apache2/sites-enabled/000-default.conf
/etc/nginx/sites-available/000-default.conf
/etc/nginx/nginx.conf
/var/auth/log
```
Windows:
```
C:/boot.ini
```

## Postgresql
Files are commonly only accessible by postgresql user.
Nonetheless, here are the common directories:
- /usr/local/pgsql/data/mydb/mytable.table
- /var/lib/postgresql/