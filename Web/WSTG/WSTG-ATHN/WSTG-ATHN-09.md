# Testing for Weak Password Change or Reset Functionalities

## General Concerns

- [ ] Is the password reset process weaker than the authentication process?
- [ ] Is there rate limiting or other protection agains automated attacks?
- [ ] Is it vulnerable to common attacks? (XSS, SQLi, SSRF)
- [ ] Does the reset process allow user enumeration?
- [ ] Does the website disclose senstiive information, such as the reset token?

## Email - New Password Sent

- [ ] Is the user forced to change the password on initial login?
- [ ] Is the password security generated?
- [ ] Is the user's existing password sent to them?
- [ ] Are the emails sent from a domain with anti-spoofing protection?
- [ ] Is the email considered sufficiently secure?

## Email - Link Sent

- [ ] Does the link use HTTPS?
- [ ] Can the link be used multiple times?
- [ ] Does the link expire if it remains unused?
- [ ] Is the token sufficiently long and random?
- [ ] Does the link contain a user ID?
- [ ] Can you inject a different host header? (See [[#Host injection]])
- [ ] Is the link exposed to third parties?
- [ ] Are the emails sent from a domain with anti-spoofing protection?
- [ ] Is the email considered sufficiently secure?

## Host injection

1. Find a valid user e-mail
2. Send a password reset request, but intercept the request
3. Change the `Host` by something controlled by you
4. Wait for the user to click the link and receive the valid token

## Token Sent Over SMS or Phone Call

- [ ] Is the token sufficiently long and random?
- [ ] Can the token be used multiple times?
- [ ] Does the token expire if it remains unused?
- [ ] Are appropriate rate limiting and restrictions in place?
- [ ] Is SMS or a phone call considered sufficiently secure?

## Security Questions

Rather than sending them a link or new password, security questions can be used as a mechanism to authenticate the user. This is considered to be a weak approach, and should not be used if better options are available.

See [[WSTG-ATHN-08]] for further information.

## Authenticated Password Changes

- [ ] When setting the password, can you specify the user ID?
- [ ] Is the user required to re-authenticate?
- [ ] Is the password change vulnerable to CSRF?
- [ ] Is a strong and effective password policy applied?

## References
- https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/09-Testing_for_Weak_Password_Change_or_Reset_Functionalities
- https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning