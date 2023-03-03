## Checklist by attack type

- [ ] Prototype pollution
- [ ] JWT tokens
- [ ] SQL injection
- [ ] XSS - Cross-site scripting
- [ ] CSRF - Cross-site request forgery
- [ ] XXE - XML external entity injection
- [ ] Clickjacking
- [ ] CORS - Cross-origin resource sharing
- [ ] SSRF - Server-side request forgery
- [ ] HTTP Request Smuggling
- [ ] OS command injection
- [ ] SSTI - Server-side template injection
- [ ] Insecure deserialization
- [ ] Directory traversal
- [ ] Access control and privilege escalation
- [ ] Authentication vulnerabilities (including OAuth 2.0)
- [ ] Business logic vulnerabilities
- [ ] Websockets vulnerabilities
- [ ] DOM-based vulnerabilities
- [ ] Web cache poisoning
- [ ] HTTP Host header attacks
- [ ] Information disclosure vulnerabilities
- [ ] File upload vulnerabilities

## Checklist by vulnerability type

- RCE - Remote command execution
	- [ ] SQL injection
	- [ ] SSTI - Server-side template injection	
	- [ ] XME - XML external entity injection
	- [ ] Insecure deserialization
	- [ ] OS command injection
	- [ ] File upload vulnerabilities
 - Client-side attacks
	- [ ] Clickjacking
	- [ ] CSRF - Cross-site request forgery
	- [ ] CORS - Cross-origin resource sharing
- Poisoning
	- [ ] HTTP Request Smuggling
	- [ ] HTTP Host header attacks
	- [ ] Web cache poisoning
- Broken access control
	- [ ] JWT tokens
	- [ ] Access control and privilege escalation
	- [ ] Insecure deserialization
	- [ ] XXE - XML external entity injection
	- [ ] Directory traversal
	- [ ] Authentication vulnerabilities (including OAuth 2.0)
	- [ ] Business logic vulnerabilities
	- [ ] File upload vulnerabilities
	- [ ] SSRF - Server-side request forgery
- Server-side attacks which can lead to multiple exploits
	- [ ] Prototype pollution
	- [ ] Websockets vulnerabilities
	- [ ] DOM-based vulnerabilities
	- [ ] Information disclosure vulnerabilities
	- [ ] XSS - Cross-site scripting

## Information Disclosure

- [ ] Hidden directories or well-known directories (robots.txt, .well-known, etc...)
- [ ] Backup files, git repositories, zip files
- [ ] Verbose error messages
- [ ] Unnecessary highly sensitive information, such as credit cards
- [ ] Hard coded information (API keys, IP addresses, database credentials)
- [ ] Different or specific responses when hitting invalid resources (e.g. "username not found")

## CORS

- [ ] These headers must be present in the response:
	- [ ] `Access-Control-Allow-Origin: https://website.com`
	- [ ] `Access-Control-Allow-Credentials: true`
- [ ] Custom origin is reflected on web server
- [ ] Check for ACAO whitelist rules (prepend or append stuff on allowed origin)
- [ ] Test `null` Origin
- [ ] XSS via CORS trust relationships
- [ ] Break TLS if CORS allows http version of vulnerable website
- [ ] Reach intranet

## CSRF

- [ ] CSRF token
	- [ ] Change request type (e.g. from GET to POST)
	- [ ] Do not include CSRF token
	- [ ] Use a known CSRF token
	- [ ] Use associated CSRF cookies (e.g. csrfKey)
- [ ] SameSite Restrictions
	- [ ] Strict
		- [ ] Use a gadget on the website that allows dynamic redirection
	- [ ] Lax
		- [ ] Use GET request instead of post
		- [ ] Override method by using `_method` on form
		- [ ] Refresh user cookie by sending a request to SSO login first
	- [ ] General
		- [ ] Use a sibling domain (e.g. .net instead of .com)
		- [ ] Try CSWSH (cross-site websocket hijacking)

## Clickjacking

- [ ] Check for CSP or X-Frame-Options headers
- [ ] XSS through clickjacking
- [ ] Prefilled from using GET parameters
- [ ] Multi-step clicjacking
- [ ] Malicious actions (delete, change password, etc...)


## Broken Access Control

- [ ] Unprotected functionality: user can perform tasks that only admin should
- [ ] Parameter based: user can append admin parameter, e.g. `?admin=true` 
- [ ] Platform misconfiguration: user can bypass `DENY` restriction with `X-Original-URL` or `X-Rewrite-URL`
- [ ] Rewrite cookie
- [ ] Check requests sent to see if hidden information in being sent
- [ ] Fuzz with parameters
- [ ] Multi-step processes (before hitting "confirm" if there is a prompt, connect another session and submit unprivileged cookie)
- [ ] Check if `Referer` header is used on high-privileged tasks

## Insecure Deserialization

- [ ] Look for serialized data anywhere in the website (Burp Scanner does that)
	- [ ] PHP: `O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}`
	- [ ] Java: begin with `ac ed` hex bytes or `rO0` base64; also the class `java.io.Serializable` and the object `readObject()`
- [ ] Modify object attributes
- [ ] Modify data types `== or ===`
- [ ] Use application functionality
- [ ] Arbritrary object injection
- [ ] Ysoserial/PHPGGC (trigger error to find out framework)

## SSTI

- [ ] Polyglot: `${{<%[%'"}}%\`
- [ ] Plaintext context: `Hello ${7*7}` --> `Hello 49`
- [ ] Code context: `data.username` --> `Hello Carlos`

## HTTP Request Smuggling

- [ ] Validation:
	- [ ] Use HTTP Request Smuggler extension
	- [ ] (CL.TE) Content-Length -> Transfer-Encoding
		- [ ] set CL to 4
		- [ ] end request with `/r/n/r/n` after `0`
	- [ ] (TE.CL) Transfer-Encoding -> Content-Length
		- [ ] leave extra space on `Content-Length` in smuggled request
	- [ ] (TE.TE) Transfer-Encoding -> Transfer-Encoding
- [ ] Exploits:
	- [ ] bypass front-end security controls
	- [ ] reveal front-end request rewriting
	- [ ] capture other users' request if the HTTP-RS happen on stored data
	- [ ] exploit reflected XSS
	- [ ] redirect user to OAST by sumuggling  `Host` header
	- [ ] response queue poisoning (cause 404 on next request to better validate)
	- [ ] CL. 0 attacks
	- [ ] client-side desync
- [ ] HTTP/2 Exploits:
	- [ ] downgrading (front-end HTTP/2, back-end HTTP/1.1)
	- [ ] CRLF injection on header names
	- [ ] pseudo-header injection
	- [ ] request splitting
	- [ ] request tunnelling
- [ ] If you find this in the wild, check the [[Finding HTTP-RS#Note|notes]] for proper validation and poc

## SSRF

- [ ] Poison POST request to reach localhost
- [ ] Poison POST request to reach other back-end/internal host
- [ ] Attack types
	- [ ] SSRF against the server
	- [ ] SSRF agains the other back-end
- [ ] Bypasses
	- [ ] Bypass blacklist filters
	- [ ] Bypass whitelist filters
	- [ ] Bypass filters via open redirection
- [ ] Blind attacks
	- [ ] use OAST for validation
	- [ ] try known exploits (e.g. shellshock)
	- [ ] use partial URLs
	- [ ] use URL in data formats (e.g. in XML)
	- [ ] poison the Referer header with your OAST

## JWT

- [ ] Flawed JWT signature verification
	- [ ] The web server does not verify signature at all
	- [ ] The attacker can set signature to none (leave a trailing dot!)
- [ ] Brute force attacks
- [ ] JWT header parameter injections
	- [ ] jwt: the attacker can inject their own key by producing a RSA signature
	- [ ] jku: the attacker can store jwt on their own web server
	- [ ] kid: the attacker can inject the key path (directory traversal) 
	- [ ] cty and x5c: interesting parameters, further analysis required.
- [ ] Algorithm confusion
	- [ ] sign the token with a symmetric algorithm although the server was expecting an asymmetric algorithm.
	- [ ] Obtain the server's public key
	- [ ] Convert the public key to a suitable format (base64)
	- [ ] Create a malicious JWT with a modified payload and the alg header set to HS256.
	- [ ] Sign the token with HS256, using the public key as the secret.

## XXE

- [ ] File Inclusion (classic XXE)
- [ ] SSRF
- [ ] Blind XXE
	- [ ] OAST attacks
	- [ ] Error-based attacks
	- [ ] Local DTD attacks
- [ ] Hidden attacks
	- [ ] XInclude
	- [ ] File upload
	- [ ] Modified content-type