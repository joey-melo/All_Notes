## Serialization/Unserialization
- [ ] https://snoopysecurity.github.io/web-application-security/2021/01/08/02_php_object_injection_exploitation-notes.html
### References
- Injecto - Proving Grounds
- Emporium - Proving Grounds

## Type juggling
The value `QNKCDZO` in md5 is `0e830400451993494058024219903391`. You can abuse this if you know the md5 value of a given hash starts with "0e". This is vulnerable in case you see php code using `!=`. For example: `if (!$user || (md5($password) != $user['password'])) {` ^5e1b8a

If you encounter `==` in php code, you can bypass it.
```
username=admin&password[]=
```
### References
- Potato - Vulnhub (https://www.infosecarticles.com/potato-1-vulnhub-walkthrough)