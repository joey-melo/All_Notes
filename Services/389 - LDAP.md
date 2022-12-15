## LDAP Ports
- 389
- 636
- 3268
- 3269

## LDAPsearch
### Options
```shell
-x # simple authentication
-H # URI
-b # base dn for search
-s # search scope
-p # port
-D # bind DN (domain\\user)
-w # password
-v # verbose
```
### Sample Queries
```bash
ldapsearch -x -H ldap://<ip> -b "dc=asky,dc=corp" # basic query
ldapserach -x -H ldap://<ip> -b "dc=support,dc=htb" -D <domain\\user> -w <pass> # authenticated query
ldapsearch -x -H ldap://<ip> -s base.namingcontexts # scope search
ldapsearch -x -p 389 -H ldap://<ip> -b "dc=example,dc=local" -s sub "*" | grep lock # check account lockout policy
```

## Python Queries
```python
import ldap3
server = ldap3.Server('x.X.x.X', get_info = ldap3.ALL, port =636, use_ssl = True)
connection = ldap3.Connection(server)
connection.bind()
print(server.info)

# Dump all objects
connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=*))', search_scope='SUBTREE', attributes='*')
print(connection.entries)

# Dump whole LDAP:
connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=person))', search_scope='SUBTREE', attributes='userPassword')
print(connection.entries)

```