
## CSP: Content Security Policy

CSP is a browser security mechanism that aims to mitigate [XSS](https://portswigger.net/web-security/cross-site-scripting) and some other attacks. It works by restricting the resources (such as scripts and images) that a page can load and restricting whether a page can be framed by other pages.

To enable CSP, a response needs to include an HTTP response header called `Content-Security-Policy` with a value containing the policy. The policy itself consists of one or more directives, separated by semicolons.

The following directive will only allow scripts to be loaded from the [same origin](https://portswigger.net/web-security/cors/same-origin-policy) as the page itself:

```
script-src 'self'
img-src 'self'
frame-ancestors 'self'
frame-ancestors 'none'
```


The following directives will only allow scripts to be loaded from a specific domain:

```
script-src https://scripts.normal-website.com
img-src https://images.normal-website.com
frame-ancestors 'self' https://normal-website.com https://*.robust-website.com
```

## Encode data on output

Encoding should be applied directly before user-controllable data is written to a page, because the context you're writing into determines what kind of encoding you need to use. For example, values inside a JavaScript string require a different type of escaping to those in an HTML context.

In an HTML context, you should convert non-whitelisted values into HTML entities:

-   `<` converts to: `&lt;`
-   `>` converts to: `&gt;`

In a JavaScript string context, non-alphanumeric values should be Unicode-escaped:

-   `<` converts to: `\u003c`
-   `>` converts to: `\u003e`

Sometimes you'll need to apply multiple layers of encoding, in the correct order. For example, to safely embed user input inside an event handler, you need to deal with both the JavaScript context and the HTML context. So you need to first Unicode-escape the input, and then HTML-encode it:

`<a href="#" onclick="x='This string needs two layers of escaping'">test</a>`

## Validate input on arrival

Encoding is probably the most important line of XSS defense, but it is not sufficient to prevent XSS vulnerabilities in every context. You should also validate input as strictly as possible at the point when it is first received from a user.

Examples of input validation include:

-   If a user submits a URL that will be returned in responses, validating that it starts with a safe protocol such as HTTP and HTTPS. Otherwise someone might exploit your site with a harmful protocol like `javascript` or `data`.
-   If a user supplies a value that it expected to be numeric, validating that the value actually contains an integer.
-   Validating that input contains only an expected set of characters.

Input validation should ideally work by blocking invalid input. An alternative approach, of attempting to clean invalid input to make it valid, is more error prone and should be avoided wherever possible.