# Reflected XSS into HTML context with nothing encoded

## Description
The application is vulnerable to Reflected Cross-Site Scripting (XSS). User-supplied input from the search parameter is reflected directly into the HTML response without proper encoding or sanitization, allowing arbitrary JavaScript execution in a victim's browser.

## Steps to Exploit
1. Navigate to the product search functionality.
2. Perform a search and observe that the search term is reflected in the response.
3. Intercept or modify the search request using Burp Suite.
4. Replace the search term with:
   ```html
   <script>alert(document.domain)</script>
   ```
5. Send the request.
6. Observe that the JavaScript executes in the browser.
7. Craft a malicious URL containing the payload and distribute it to potential victims.

## Proof of Concept

### Payload
```html
<script>alert(document.domain)</script>
```

### Malicious URL
```text
/search?q=<script>alert(document.domain)</script>
```

### Reflected Response
```html
<p>Search results for: <script>alert(document.domain)</script></p>
```

## Impact
- Execution of arbitrary JavaScript in victim browsers.
- Session theft through access to sensitive client-side data.
- Phishing and credential harvesting via malicious page content.
- Defacement or manipulation of application content viewed by users.

## Remediation
1. Apply context-aware output encoding to all user-supplied data.
2. Implement a strict Content Security Policy (CSP).
3. Set the `HttpOnly` flag on session cookies.
4. Use frameworks that automatically escape HTML output.

## CVSS Score
**CVSS v3.1: 6.1 (Medium)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N`

## CVSS Justification
- **Attack Vector:** Network – delivered through a crafted URL.
- **Attack Complexity:** Low – requires a simple payload.
- **Privileges Required:** None.
- **User Interaction:** Required – the victim must visit the malicious link.
- **Scope:** Changed – execution occurs within the victim's browser context.
- **Confidentiality:** Low – sensitive information such as session data may be exposed.
- **Integrity:** Low – attackers can manipulate page content and user interactions.
- **Availability:** None – no direct impact on application availability.