
Response queue poisoning is a powerful form of request smuggling attack that causes a front-end server to start mapping responses from the back-end to the wrong requests. In practice, this means that all users of the same front-end/back-end connection are persistently served responses that were intended for someone else.

This is achieved by smuggling a complete request, thereby eliciting two responses from the back-end when the front-end server is only expecting one.

## How to construct a response queue poisoning attack

For a successful response queue poisoning attack, the following criteria must be met:

-   The TCP connection between the front-end server and back-end server is [reused for multiple request/response cycles](https://portswigger.net/web-security/request-smuggling#what-happens-in-an-http-request-smuggling-attack).
    
-   The attacker is able to successfully smuggle a complete, standalone request that receives its own distinct response from the back-end server.
    
-   The attack does not result in either server closing the TCP connection. Servers generally close incoming connections when they receive an invalid request because they can't determine where the request is supposed to end.

If you smuggle a request that also contains a body, the next request on the connection will be appended to the body of the smuggled request. This often has the side-effect of truncating the final request based on the apparent `Content-Length`. As a result, the back-end effectively sees three requests, where the third "request" is just a series of leftover bytes:

Front-end (CL)

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 120
Transfer-Encoding: chunked

0

POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 25

x=GET / HTTP/1.1
Host: vulnerable-website.com
```

Back-end (TE)

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 120
Transfer-Encoding: chunked

0

POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: x-www-form-urlencoded
Content-Length: 25

x=GET / HTTP/1.1
Host: v (ulnerable-website.com)
```

As these leftover bytes don't form a valid request, this typically results in an error, causing the server to close the connection.

With a bit of care, you can smuggle a complete request instead of just a prefix. As long as you send exactly two requests in one, any subsequent requests on the connection will remain unchanged.

When you smuggle a complete request, the front-end server still thinks it only forwarded a single request. On the other hand, the back-end sees two distinct requests, and will send two responses accordingly.

To make it easier to differentiate stolen responses from responses to your own requests, try using a non-existent path in both of the requests that you send. That way, your own requests should consistently receive a 404 response, for example.