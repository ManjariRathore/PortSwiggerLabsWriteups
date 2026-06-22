# Stored XSS into HTML context with nothing encoded

## Description
The blog comment functionality is vulnerable to Stored Cross-Site Scripting (XSS). User-supplied content is stored and rendered without proper sanitization or encoding, allowing malicious JavaScript to execute whenever users view the affected page.

## Steps to Exploit
1. Navigate to a blog post that allows comments.
2. Enter the following payload in the comment field:
   ```html
   <script>alert(document.domain)</script>
   ```
3. Submit the comment.
4. Visit the blog post page.
5. Observe that the JavaScript executes automatically when the page loads.

## Proof of Concept

### Payload
```html
<script>alert(document.domain)</script>
```

### Effect
```text
The payload is stored in the database and executes automatically for every user who views the affected page.
```

## Impact
- Persistent JavaScript execution in visitors' browsers.
- Potential theft of session tokens and sensitive user data.
- Phishing and credential harvesting attacks.
- Malicious content delivered automatically to all page visitors.

## Remediation
1. Apply context-aware HTML encoding before rendering user content.
2. Implement a strict Content Security Policy (CSP).
3. Validate and sanitize user input using a trusted HTML sanitization library.
4. Mark session cookies as `HttpOnly` and `Secure`.

## CVSS Score
**CVSS v3.1: 6.1 (Medium)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through a web form.
- **Attack Complexity:** Low – requires only a crafted payload.
- **Privileges Required:** None.
- **User Interaction:** Required – victims must visit the affected page.
- **Scope:** Changed – malicious code executes in users' browser contexts.
- **Confidentiality:** Low – may expose session data and user information.
- **Integrity:** Low – attackers can modify page content and user interactions.
- **Availability:** None – no direct impact on service availability.