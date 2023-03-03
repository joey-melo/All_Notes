## Reflected XSS

Where the malicious script comes from the current HTTP request.

```
https://insecure-website.com/status?message<script>/*+Bad+stuff+here...+*/</script> 
<p>Status: <script>/* Bad stuff here... */</script></p>
```

## Stored XSS

Where the malicious script comes from the website database.

```
// for example, this would be stored in a comment section:
<p><script>/* Bad stuff here... */</script></p>
```

## DOM-based XSS

Where the vulnerability exists in client-side code rather than server-side code.

```javascript
// vulnerable code
var search = document.getElementById('search').value;
var results = document.getElementById('results');
results.innerHTML = 'You searched for: ' + search;
```

```
// payload delivered
`You searched for: <img src=1 onerror='/* Bad stuff here... */'>`
```