# Basic server-side template injection (code context)

## Description
The application is vulnerable to Server-Side Template Injection (SSTI) in the blog author display setting. User-controlled input is embedded directly into a Python Tornado template expression, allowing attackers to inject template directives and execute arbitrary Python code on the server.

## Steps to Exploit
1. Log in to the application and navigate to **Account Settings**.
2. Locate the blog post author display setting.
3. Intercept the update request using Burp Suite.
4. Modify the `blog-post-author-display` parameter with a malicious template payload.
5. Forward the request.
6. Navigate to a blog post.
7. Observe the command output rendered within the author display field.

## Proof of Concept

### Payload
```python
}}{% import os %}{{ os.system('whoami') }}
```

### Effect
```text
The os.system() function is executed on the server and the command output is rendered in the author display field.
```

## Impact
- Remote Code Execution (RCE) on the application server.
- Unauthorized access to sensitive files, environment variables, and application secrets.
- Potential data exfiltration and privilege escalation.
- Ability to establish persistent access to the compromised system.

## Remediation
1. Never embed user-controlled input directly into template expressions.
2. Pass user input as data through template context variables.
3. Enable Tornado's autoescaping and secure template features.
4. Validate and sanitize all user-supplied input before rendering.
5. Monitor application logs for suspicious template execution activity.

## CVSS Score
**CVSS v3.1: 8.8 (High)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H`

## CVSS Justification
- **Attack Vector:** Network – exploitable remotely through application functionality.
- **Attack Complexity:** Low – requires only a crafted template payload.
- **Privileges Required:** Low – requires an authenticated user account.
- **User Interaction:** None.
- **Confidentiality:** High – attacker can access sensitive data and secrets.
- **Integrity:** High – arbitrary code execution allows modification of application and system resources.
- **Availability:** High – attacker can disrupt services or execute destructive commands.