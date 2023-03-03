## Definitions

**DOM-Based XXS**: when the web application's client-side code dynamically updates the content of a web page based on user-supplied data.

**Source:** any user-controllable input that allows to exploit the prototype pollution. For example: URL parameters, text fields, etc...

**Gadget:** these are functions that could be abused to pollute the prototype and execute malicious payloads. For example: `eval(vuln)`, `document.body.appendChild(vuln)`.

**Sink:** areas in a website's code that are vulnerable to DOM-based cross-site scripting. For example: `eval(myObj('Hello Mr, ' + myObj.pollutedProto));`.

