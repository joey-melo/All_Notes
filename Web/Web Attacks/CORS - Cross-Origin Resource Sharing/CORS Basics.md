## What is CORS (cross-origin resource sharing)?

Cross-origin resource sharing (CORS) is a browser mechanism which enables controlled access to resources located outside of a given domain. It extends and adds flexibility to the same-origin policy ([SOP](https://portswigger.net/web-security/cors/same-origin-policy)). However, it also provides potential for cross-domain attacks, if a website's CORS policy is poorly configured and implemented. CORS is not a protection against cross-origin attacks such as [cross-site request forgery](https://portswigger.net/web-security/csrf) ([CSRF](https://portswigger.net/web-security/csrf)).

## Same-Origin Policy (SOP)

The same-origin policy restricts scripts on one origin from accessing data from another origin.

Consider the following URL:

`http://normal-website.com/example/example.html`

Where:
- scheme = `http`
- domain = `normal-website.com`
- port = `80`

The following table shows how the same-origin policy will be applied if content at the above URL tries to access other origins:

`http://normal-website.com/example/ `	Access permited: same scheme, domain, and port
`http://normal-website.com/example2/ `	Access permited: same scheme, domain, and port
`https://normal-website.com/example/ `	Access denied: different scheme and port
`http://en.normal-website.com/example/ `	Access denied: different domain
`http://www.normal-website.com/example/ `	Access denied: different domain
`http://normal-website.com:8080/example/ `	Access denied: different port

The same-origin policy generally controls the access that JavaScript code has to content that is loaded cross-domain. Cross-origin loading of page resources is generally permitted. For example, the SOP allows embedding of images via the `<img>` tag, media via the `<video>` tag and JavaScript includes with the `<script>` tag. However, while these external resources can be loaded by the page, any JavaScript on the page won't be able to read the contents of these resources.

There are various exceptions to the same-origin policy:

-   Some objects are writable but not readable cross-domain, such as the `location` object or the `location.href` property from iframes or new windows.
-   Some objects are readable but not writable cross-domain, such as the `length` property of the `window` object (which stores the number of frames being used on the page) and the `closed` property.
-   The `replace` function can generally be called cross-domain on the `location` object.
-   You can call certain functions cross-domain. For example, you can call the functions `close`, `blur` and `focus` on a new window. The `postMessage` function can also be called on iframes and new windows in order to send messages from one domain to another.

## Access-Control-Allow-Origin (ACAO)

The `Access-Control-Allow-Origin` header is included in the response from one website to a request originating from another website, and identifies the permitted origin of the request. A web browser compares the Access-Control-Allow-Origin with the requesting website's origin and permits access to the response if they match.

For example, suppose a website with origin `normal-website.com` causes the following cross-domain request:

```http
GET /data HTTP/1.1 
Host: robust-website.com 
Origin : https://normal-website.com
```

```http
HTTP/1.1 200 OK 
Access-Control-Allow-Origin: https://normal-website.com`
```

The default behavior of cross-origin resource requests is for requests to be passed without credentials like cookies and the Authorization header. However, the cross-domain server can permit reading of the response when credentials are passed to it by setting the CORS `Access-Control-Allow-Credentials` header to true. Now if the requesting website uses JavaScript to declare that it is sending cookies with the request:

```http
GET /data HTTP/1.1 
Host: robust-website.com
Origin: https://normal-website.com 
Cookie: JSESSIONID=<value>
```

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://normal-website.com 
Access-Control-Allow-Credentials: true
```

Although wildcards are permitted on ACAO, you **can't** combine it with ACAC, as such:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: * 
Access-Control-Allow-Credentials: true
```

Under certain circumstances, when a cross-domain request includes a non-standard HTTP method or headers, the cross-origin request is preceded by a request using the `OPTIONS` method, and the CORS protocol necessitates an initial check on what methods and headers are permitted prior to allowing the cross-origin request. This is called the pre-flight check.

```http
OPTIONS /data HTTP/1.1
Host: <some website>
Origin: https://normal-website.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Special-Request-Header
```

```http
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://normal-website.com
Access-Control-Allow-Methods: PUT, POST, OPTIONS
Access-Control-Allow-Headers: Special-Request-Header
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 240
```