## Summary

1. Test if validation of `Referer` depends on the eader being present
2. Test if validation of `Referer` can be bypassed by simply appending the site to your request as a parameter

## Referer header

Aside from defenses that employ CSRF tokens, some applications make use of the HTTP `Referer` header to attempt to defend against CSRF attacks, normally by verifying that the request originated from the application's own domain. This approach is generally less effective and is often subject to bypasses.

## Validation of Referer depends on header being present

Some applications validate the `Referer` header when it is present in requests but skip the validation if the header is omitted.

In this situation, an attacker can craft their [CSRF exploit](https://portswigger.net/web-security/csrf) in a way that causes the victim user's browser to drop the `Referer` header in the resulting request. There are various ways to achieve this, but the easiest is using a META tag within the HTML page that hosts the [CSRF attack](https://portswigger.net/web-security/csrf):

`<meta name="referrer" content="never">`\

## Validation of Referer can be circumvented

Some applications validate the `Referer` header in a naive way that can be bypassed. For example, if the application validates that the domain in the `Referer` starts with the expected value, then the attacker can place this as a subdomain of their own domain:

`http://vulnerable-website.com.attacker-website.com/csrf-attack`

Likewise, if the application simply validates that the `Referer` contains its own domain name, then the attacker can place the required value elsewhere in the URL:

`http://attacker-website.com/csrf-attack?vulnerable-website.com`