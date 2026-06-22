# CSRF where token validation depends on request method

## Description
The application validates CSRF tokens only for POST requests. By changing the request method to GET, the same state-changing functionality can be accessed without CSRF validation, allowing attackers to perform unauthorized actions on behalf of authenticated users.

## Steps to Exploit
1. Log in and capture the email change request using Burp Suite.
2. Observe that a CSRF token is included in the POST request.
3. Send the request to Burp Repeater.
4. Change the request method from `POST` to `GET`.
5. Move the email parameter to the query string and remove the CSRF token.
6. Forward the modified request.
7. Observe that the email address is updated successfully.
8. Craft a malicious URL containing the payload and send it to a victim.

## Proof of Concept

### Original Request
```http
POST /my-account/change-email

email=test@test.com&csrf=TOKEN123
```

### Bypassed Request
```http
GET /my-account/change-email?email=attacker@evil.com HTTP/1.1
```

### Effect
```text
The email address is updated without a valid CSRF token.
```

## Impact
- Complete bypass of CSRF protection.
- Unauthorized modification of user account settings.
- Potential account takeover through password reset functionality.
- Loss of account integrity and user trust.

## Remediation
1. Validate CSRF tokens on all state-changing requests regardless of HTTP method.
2. Reject GET requests for operations that modify application state.
3. Implement `SameSite` cookie protections as a defense-in-depth measure.
4. Review all endpoints to ensure consistent CSRF enforcement.

## CVSS Score
**CVSS v3.1: 6.5 (Medium)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:N/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through a crafted URL.
- **Attack Complexity:** Low – requires only modification of the request method.
- **Privileges Required:** None.
- **User Interaction:** Required – the victim must visit the malicious URL.
- **Confidentiality:** None – no direct disclosure of sensitive information.
- **Integrity:** High – attackers can modify account settings and potentially gain account control.
- **Availability:** None – no impact on service availability.