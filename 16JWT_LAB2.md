# JWT authentication bypass via flawed signature verification

## Description
The application accepts JWTs using the `none` algorithm, allowing tokens to be processed without signature verification. An attacker can modify token claims, remove the signature, and gain unauthorized access by presenting a forged JWT.

## Steps to Exploit
1. Log in and obtain the JWT from the session cookie.
2. Decode the JWT header and payload.
3. Change the `alg` value in the header from `RS256` to `none`.
4. Modify the `sub` claim in the payload to `administrator`.
5. Re-encode the header and payload using Base64URL encoding.
6. Remove the signature and construct the token with a trailing period.
7. Replace the session cookie with the forged JWT.
8. Access privileged functionality such as `/admin`.

## Proof of Concept

### Original Header
```json
{
  "alg": "RS256",
  "typ": "JWT"
}
```

### Forged Header
```json
{
  "alg": "none",
  "typ": "JWT"
}
```

### Forged Token Format
```text
<base64url(header)>.<base64url(payload)>.
```

### Result
```text
The server accepts the unsigned JWT and grants administrator privileges.
```

## Impact
- Complete bypass of JWT signature verification.
- Forged tokens accepted as legitimate.
- Privilege escalation to any user account, including administrators.
- Authentication and authorization controls can be completely bypassed.

## Remediation
1. Explicitly reject tokens using the `none` algorithm.
2. Enforce a strict allowlist of approved signing algorithms.
3. Use a secure JWT library that disables unsecured algorithms by default.
4. Regularly review JWT configurations and update libraries to address known vulnerabilities.

## CVSS Score
**CVSS v3.1: 9.1 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through JWT manipulation.
- **Attack Complexity:** Low – requires only modification of JWT header and payload values.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – attackers can access sensitive data belonging to any user.
- **Integrity:** High – attackers can perform actions with elevated privileges.
- **Availability:** None – no direct impact on service availability.

