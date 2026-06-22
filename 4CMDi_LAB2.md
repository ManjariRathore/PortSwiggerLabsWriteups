# Blind OS command injection with time delays

## Description
The feedback form is vulnerable to blind OS command injection. User input supplied through the `email` parameter is passed to a shell command without proper sanitization. Although command output is not returned, successful exploitation can be confirmed through a measurable response delay.

## Steps to Exploit
1. Navigate to the feedback form.
2. Populate all required fields with valid values.
3. Intercept the request using Burp Suite.
4. Locate the `email` parameter.
5. Replace its value with:
   ```bash
   || ping -c 10 127.0.0.1 ||
   ```
6. Forward the request.
7. Observe a response delay of approximately 10 seconds.

## Proof of Concept

### Payload
```bash
|| ping -c 10 127.0.0.1 ||
```

### Result
```text
Application response delayed by approximately 10 seconds.
```

## Impact
- Confirms arbitrary command execution on the server.
- May lead to full system compromise.
- Potential exposure of sensitive data.
- Risk of internal network reconnaissance and reverse shell execution.

## Remediation
1. Never pass user input directly to shell execution functions.
2. Validate and sanitize all user-supplied input.
3. Use application-native libraries instead of shell commands where possible.
4. Monitor unusual response delays and outbound network traffic.

## CVSS Score
**CVSS v3.1: 7.5 (High)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable via HTTP requests.
- **Attack Complexity:** Low – no special conditions required.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – may allow access to sensitive information.
- **Integrity:** None – no modification demonstrated.
- **Availability:** None – no service disruption demonstrated.