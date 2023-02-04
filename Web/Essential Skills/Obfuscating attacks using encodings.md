## Summary

The following are examples of encoding the letter "a":

- URL encoding: `%61`
- Double URL encoding: `%2561`
- HTML encoding: 
	- Hex: `&#x61;`
	- Decimal: `&#58;`
	- Leading zeros: `&#00000000000000058;`
- XML: `same as HTML`
- Unicode: 
	- Classic: `\u0061`
	- ES56 `\u{61}`
- Hex escape: `\x61`
- Octal escape: `\141`
- CHAR: `CHAR(61)`

Keep in mind:
- [ ] Analyze the context of the decoding and how server-side is probably decoding your input
- [ ] Decoding discrepancies may happen (server-side decoding is different than sql decoding on back-end), we may use that in our favor
- [ ] Obfuscate your payload via multiple encodings (or multiple layers of encoding). [[#Obfuscation via multiple encodings]]

Tricks:
- [ ] Use **Hackvertor** extension to obfuscate payloads easier.

## Context-specific decoding

Think about how the website is decoding the contet you are providing. For example, a query parameter is tipically URL decoded server-side, while the text content of an HTML element may be HTML decoded client-side.

## Decoding discrepancies

A secure will website must check if the input decoding is the same decoding performed by the back-end server. For example, if a user is able to pass a SQL statement via a URL query, the decoding used must be the same that is in use for the SQL server.

## Obfuscation via URL encoding

You can inject URL-encoded data via the URL and it will still be interpreted correctly by the back-end application. So, for example, if the web client may block the word `SELECT`, but an attacker might still inject `%53%45%4C%45%43%54`

## Obfuscation via double URL encoding

Let's say the web client blocks your attempts of injecting `<` or `%3C`. There is a chance that you can still make the attack work by encoding the `%` sign, which is `%25`. In a vulnerable application, if you send `%253C`, the web server will decode it to become `%2C`, which will be interpreted as `<` by the back-end.

## Obfuscation via HTML encoding

HTML has specific references for characters, for example `&colon;` represents the `,` character.

Additionally, you can use the hex code point of the character wrapped in `&# ;`. For instance, the hex code of `a` is `0x61`, so the HTML encoding of the character is `&#x61;`. If a WAF filters the word `alert` for example, you might be able to inject `&#x61;lert`.

### Leading zeros

Interestingly, you may use leading zeros in HTML encoding to trick WAFs. So instead of replacing `a` with `&#58;`, you could try with `&#00000000000000000058;`

## Obfuscation via XML encoding

XML supports HTML character encoding, so ti may be useful to bypass WAFs

```xml
<stockCheck>
    <productId>
        123
    </productId>
    <storeId>
        999 &#x53;ELECT * FROM information_schema.tables
    </storeId>
</stockCheck>
```

## Obfuscation via unicode escaping

Unicode escape consists of the prefix `\u` followed by the four-digit hex code. For example `\u003a` represents a colon `:`.

ES56 also supports curly braces: `\u{3a}`.

The following might bypass WAFs: `eval("\u0061lert(1)")`

## Obfuscation via multiple encodings

It's important to combine multiple layers of encoding to hide your payloads. Obsertve the following example:

```javascript
<a href="javascript:&bsol;u0061lert(1)">Click me</a>
```

First, the browser will decode `&bsol;` into a backslash `\`, resulting in:

```javascript
<a href="javascript:\u0061lert(1)">Click me</a>
```

Then, it will decode the unicode sequence `\u0061`, resulting in:

```javascript
<a href="javascript:alert(1)">Click me</a>
```