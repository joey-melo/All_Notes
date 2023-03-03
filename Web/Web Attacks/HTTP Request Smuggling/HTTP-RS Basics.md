## What is HTTP request smuggling?

HTTP request smuggling is a technique for interfering with the way a web site processes sequences of HTTP requests that are received from one or more users. Request smuggling vulnerabilities are often critical in nature, allowing an attacker to bypass security controls, gain unauthorized access to sensitive data, and directly compromise other application users.

Essentially, the attacker is able to make part of their request to be interpreter by the back-end server as the start of the next request. For a better visual representation, check https://portswigger.net/web-security/request-smuggling

## How do HTTP request smuggling vulnerabilities arise?

Most HTTP request smuggling vulnerabilities arise because the HTTP specification provides two different ways to specify where a request ends: the `Content-Length` header and the `Transfer-Encoding` header.

The `Content-Length` header is straightforward: it specifies the length of the message body in bytes. For example:

```http
POST /search HTTP/1.1
Host: normal-website.com 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 11 

q=smuggling
```

The `Transfer-Encoding` header can be used to specify that the message body uses chunked encoding. This means that the message body contains one or more chunks of data. Each chunk consists of the chunk size in bytes (expressed in hexadecimal), followed by a newline, followed by the chunk contents. The message is terminated with a chunk of size zero. For example:

```http
POST /search HTTP/1.1
Host: normal-website.com 
Content-Type: application/x-www-form-urlencoded 
Transfer-Encoding: chunked 

b 
q=smuggling 
0
```

Since the HTTP specification provides two different methods for specifying the length of HTTP messages, it is possible for a single message to use both methods at once, such that they conflict with each other. The HTTP specification attempts to prevent this problem by stating that if both the `Content-Length` and `Transfer-Encoding` headers are present, then the `Content-Length` header should be ignored. This might be sufficient to avoid ambiguity when only a single server is in play, but not when two or more servers are chained together. In this situation, problems can arise for two reasons:

-   Some servers do not support the `Transfer-Encoding` header in requests.
-   Some servers that do support the `Transfer-Encoding` header can be induced not to process it if the header is obfuscated in some way.

If the front-end and back-end servers behave differently in relation to the (possibly obfuscated) `Transfer-Encoding` header, then they might disagree about the boundaries between successive requests, leading to request smuggling vulnerabilities.