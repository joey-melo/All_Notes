## What is clickjacking?

Clickjacking is an interface-based attack in which a user is tricked into clicking on actionable content on a hidden website by clicking on some other content in a decoy website. Consider the following example:

A web user accesses a decoy website (perhaps this is a link provided by an email) and clicks on a button to win a prize. Unknowingly, they have been deceived by an attacker into pressing an alternative hidden button and this results in the payment of an account on another site. This is an example of a clickjacking attack. The technique depends upon the incorporation of an invisible, actionable web page (or multiple pages) containing a button or hidden link, say, within an iframe. The iframe is overlaid on top of the user's anticipated decoy web page content. This attack differs from a [CSRF](https://portswigger.net/web-security/csrf) attack in that the user is required to perform an action such as a button click whereas a [CSRF attack](https://portswigger.net/web-security/csrf) depends upon forging an entire request without the user's knowledge or input.

## How to prevent clickjacking attacks

### X-Frame-Options

X-Frame-Options was originally introduced as an unofficial response header in Internet Explorer 8 and it was rapidly adopted within other browsers. The header provides the website owner with control over the use of iframes or objects so that inclusion of a web page within a frame can be prohibited with the `deny` directive:

`X-Frame-Options: deny`

Alternatively, framing can be restricted to the same origin as the website using the `sameorigin` directive

`X-Frame-Options: sameorigin`

or to a named website using the `allow-from` directive:

`X-Frame-Options: allow-from https://normal-website.com`

X-Frame-Options is not implemented consistently across browsers (the `allow-from` directive is not supported in Chrome version 76 or Safari 12 for example). However, when properly applied in conjunction with Content Security Policy as part of a multi-layer defense strategy it can provide effective protection against clickjacking attacks.

### Content Security Policy ([CSP](https://portswigger.net/web-security/cross-site-scripting/content-security-policy))

Content Security Policy (CSP) is a detection and prevention mechanism that provides mitigation against attacks such as XSS and clickjacking. CSP is usually implemented in the web server as a return header of the form:

`Content-Security-Policy: policy`

where policy is a string of policy directives separated by semicolons. The CSP provides the client browser with information about permitted sources of web resources that the browser can apply to the detection and interception of malicious behaviors.

The recommended clickjacking protection is to incorporate the `frame-ancestors` directive in the application's Content Security Policy. The `frame-ancestors 'none'` directive is similar in behavior to the X-Frame-Options `deny` directive. The `frame-ancestors 'self'` directive is broadly equivalent to the X-Frame-Options `sameorigin` directive. The following CSP whitelists frames to the same domain only:

`Content-Security-Policy: frame-ancestors 'self';`

Alternatively, framing can be restricted to named sites:

`Content-Security-Policy: frame-ancestors normal-website.com;`

To be effective against clickjacking and XSS, CSPs need careful development, implementation and testing and should be used as part of a multi-layer defense strategy.