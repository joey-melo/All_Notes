## DOM XSS via client-side prototype pollution

**Find a prototype pollution source**

1.  In your browser, try polluting the `Object.prototype` by injecting an arbitrary property via the query string:
    `/?__proto__[foo]=bar`
2.  Open the browser DevTools panel and go to the **Console** tab.
3.  Enter `Object.prototype`.
4.  Study the properties of the returned object. Observe that it now has a `foo` property with the value `bar`. You've successfully found a prototype pollution source.

**Identify a gadget**

1.  In the browser DevTools panel, go to the **Sources** tab.
2.  Study the JavaScript files that are loaded by the target site and look for any DOM XSS sinks.
3.  In `searchLogger.js`, notice that if the `config` object has a `transport_url` property, this is used to dynamically append a script to the DOM.
4.  Notice that no `transport_url` property is defined for the `config` object. This is a potential gadget for controlling the `src` of the `<script>` element.

**Craft an exploit**

1.  Using the prototype pollution source you identified earlier, try injecting an arbitrary `transport_url` property:
    `/?__proto__[transport_url]=foo`
2.  In the browser DevTools panel, go to the **Elements** tab and study the HTML content of the page. Observe that a `<script>` element has been rendered on the page, with the `src` attribute `foo`.
3.  Modify the payload in the URL to inject an XSS proof-of-concept. For example, you can use a `data:` URL as follows:
    `/?__proto__[transport_url]=data:,alert(1);`
4.  Observe that the `alert(1)` is called and the lab is solved.

## DOM XSS via an alternative prototype pollution vector

**Find a prototype pollution source**

1.  In your browser, try polluting the `Object.prototype` by injecting an arbitrary property via the query string:
    `/?__proto__[foo]=bar`
2.  Open the browser DevTools panel and go to the **Console** tab.
3.  Enter `Object.prototype`.
4.  Study the properties of the returned object and observe that your injected `foo` property has not been added.
5.  Back in the query string, try using an alternative prototype pollution vector:
    `/?__proto__.foo=bar`
6.  In the console, enter `Object.prototype` again. Notice that it now has its own `foo` property with the value `bar`. You've successfully found a prototype pollution source.

**Identify a gadget**

1.  In the browser DevTools panel, go to the **Sources** tab.
2.  Study the JavaScript files that are loaded by the target site and look for any DOM XSS sinks.
3.  Notice that there is an `eval()` sink in `searchLoggerAlternative.js`.
4.  Notice that the `manager.sequence` property is passed to `eval()`, but this isn't defined by default.

**Craft an exploit**

1.  Using the prototype pollution source you identified earlier, try injecting an arbitrary `sequence` property containing an XSS proof-of-concept payload:
    `/?__proto__.sequence=alert(1)`
2.  Observe that the payload doesn't execute.
3.  In the browser DevTools panel, go to the **Console** tab. Observe that you have triggered an error.
4.  Click the link at the top of the stack trace to jump to the line where `eval()` is called.
5.  Click the line number to add a breakpoint to this line, then refresh the page.
6.  Hover the mouse over the `manager.sequence` reference and observe that its value is `alert(1)1`. This indicates that we have successfully passed our payload into the sink, but a numeric `1` character is being appended to it, resulting in invalid JavaScript syntax.
7.  Click the line number again to remove the breakpoint, then click the play icon at the top of the browser window to resume code execution.
8.  Add trailing minus character to the payload to fix up the final JavaScript syntax:
    `/?__proto__.sequence=alert(1)-`
9.  Observe that the `alert(1)` is called and the lab is solved.

## Client-side prototype pollution in third-party libraries

1.  Load the lab in Burp's built-in browser.
2.  [Enable DOM Invader](https://portswigger.net/burp/documentation/desktop/tools/dom-invader/enabling) and [enable the prototype pollution option](https://portswigger.net/burp/documentation/desktop/tools/dom-invader/prototype-pollution#enabling-prototype-pollution).
3.  Open the browser DevTools panel, go to the **DOM Invader** tab, then reload the page.
4.  Observe that DOM Invader has identified two prototype pollution vectors in the `hash` property i.e. the URL fragment string.
5.  Click **Scan for gadgets**. A new tab opens in which DOM Invader begins scanning for gadgets using the selected source.
6.  When the scan is complete, open the DevTools panel in the same tab as the scan, then go to the **DOM Invader** tab.
7.  Observe that DOM Invader has successfully accessed the `setTimeout()` sink via the `hitCallback` gadget.
8.  Click **Exploit**. DOM Invader automatically generates a proof-of-concept exploit and calls `alert(1)`.
9.  Disable DOM Invader.
10.  In the browser, go to the lab's exploit server.
11.  In the **Body** section, craft an exploit that will navigate the victim to a malicious URL as follows:
```javascript
<script> 
	location="https://YOUR-LAB-ID.web-security-academy.net/#__proto__[hitCallback]=alert%28document.cookie%29"
</script>
```
12.  Test the exploit on yourself, making sure that you're navigated to the lab's home page and that the `alert(document.cookie)` payload is triggered.
13.  Go back to the exploit server and deliver the exploit to the victim to solve the lab.

## Client-side prototype pollution via browser APIs

**Find a prototype pollution source**

1.  In your browser, try polluting the `Object.prototype` by injecting an arbitrary property via the query string:
    `/?__proto__[foo]=bar`
2.  Open the browser DevTools panel and go to the **Console** tab.
3.  Enter `Object.prototype`.
4.  Study the properties of the returned object and observe that your injected `foo` property has been added. You've successfully found a prototype pollution source.

**Identify a gadget**

1.  In the browser DevTools panel, go to the **Sources** tab.
2.  Study the JavaScript files that are loaded by the target site and look for any DOM XSS sinks.
3.  In `searchLoggerConfigurable.js`, notice that if the `config` object has a `transport_url` property, this is used to dynamically append a script to the DOM.
4.  Observe that a `transport_url` property is defined for the `config` object, so this doesn't appear to be vulnerable.
5.  Observe that the next line uses the `Object.defineProperty()` method to make the `transport_url` unwritable and unconfigurable. However, notice that it doesn't define a `value` property.

**Craft an exploit**

1.  Using the prototype pollution source you identified earlier, try injecting an arbitrary value property:
    `/?__proto__[value]=foo`
2.  In the browser DevTools panel, go to the **Elements** tab and study the HTML content of the page. Observe that a `<script>` element has been rendered on the page, with the `src` attribute `foo`.
3.  Modify the payload in the URL to inject an XSS proof-of-concept. For example, you can use a `data:` URL as follows:
    `/?__proto__[value]=data:,alert(1);`
4.  Observe that the `alert(1)` is called and the lab is solved.

## Client-side prototype pollution via flawed sanitization

**Find a prototype pollution source**

1.  In your browser, try polluting the `Object.prototype` by injecting an arbitrary property via the query string:
    `/?__proto__.foo=bar`
2.  Open the browser DevTools panel and go to the **Console** tab.
3.  Enter `Object.prototype`.
4.  Study the properties of the returned object and observe that your injected `foo` property has not been added.
5.  Try alternative prototype pollution vectors. For example:
    `/?__proto__[foo]=bar /?constructor.prototype.foo=bar`
6.  Observe in each instance the `Object.prototype` is not modified.
7.  Go to the **Sources** tab and study the JavaScript files that are loaded by the target site. Notice that `deparamSanitized.js` uses the `sanitizeKey()` function defined in `searchLoggerFiltered.js` to strip potentially dangerous property keys based on a blocklist. However, it does not apply this filter recursively.
8.  Back in the URL, try injecting one of the blocked keys in such a way that the dangerous key remains following the sanitization process. For example:
    `/?__pro__proto__to__[foo]=bar /?__pro__proto__to__.foo=bar /?constconstructorructor.[protoprototypetype][foo]=bar /?constconstructorructor.protoprototypetype.foo=bar`
9.  In the console, enter `Object.prototype` again. Notice that it now has its own `foo` property with the value `bar`. You've successfully found a prototype pollution source and bypassed the website's key sanitization.

**Identify a gadget**

1.  Study the JavaScript files again and notice that `searchLogger.js` dynamically appends a script to the DOM using the `config` object's `transport_url` property if present.
2.  Notice that no `transport_url` property is set for the `config` object. This is a potential gadget.

**Craft an exploit**

1.  Using the prototype pollution source you identified earlier, try injecting an arbitrary `transport_url` property:
    `/?__pro__proto__to__[transport_url]=foo`
2.  In the browser DevTools panel, go to the **Elements** tab and study the HTML content of the page. Observe that a `<script>` element has been rendered on the page, with the `src` attribute `foo`.
3.  Modify the payload in the URL to inject an XSS proof-of-concept. For example, you can use a `data:` URL as follows:
    `/?__pro__proto__to__[transport_url]=data:,alert(1);`
4.  Observe that the `alert(1)` is called and the lab is solved.