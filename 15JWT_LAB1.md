# JWT authentication bypass via unverified signature

## Description
The application accepts JSON Web Tokens (JWTs) without validating their cryptographic signature. An attacker can modify the token payload to impersonate another user, including administrative accounts, and the server accepts the forged token without verification.

## Steps to Exploit
1. Log in and obtain the JWT from the session cookie.
2. Decode the JWT using Burp Suite's JWT Editor or a JWT decoding tool.
3. Modify the `sub` claim in the payload to `administrator`.
4. Re-encode the JWT without generating a valid signature.
5. Replace the original JWT in the request with the modified token.
6. Access privileged functionality such as `/admin`.
7. Observe that administrative access is granted.

## Proof of Concept

### Original JWT Payload
```json
{
  "sub": "wiener",
  "iat": 1700000000
}
```

### Forged JWT Payload
```json
{
  "sub": "administrator",
  "iat": 1700000000
}
```

### Result
```text
The server accepts the modified JWT and grants administrator privileges without validating the signature.
```

## Impact
- Horizontal and vertical privilege escalation.
- Unauthorized access to any user account.
- Full administrative access to the application.
- Bypass of authorization controls relying on JWT claims.

## Remediation
1. Verify JWT signatures on every request before trusting token contents.
2. Use a well-maintained JWT library with signature validation enabled by default.
3. Reject tokens with missing, invalid, or tampered signatures.
4. Validate security-related claims such as `exp`, `iat`, and `nbf`.

## CVSS Score
**CVSS v3.1: 9.1 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through manipulation of client-side JWTs.
- **Attack Complexity:** Low – requires only modification of JWT payload data.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – attackers can access sensitive data belonging to other users.
- **Integrity:** High – attackers can perform actions with elevated privileges.
- **Availability:** None – no direct impact on application availability.