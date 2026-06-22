# 2FA simple bypass

## Description
The application establishes an authenticated session after successful username and password verification, before validating the second authentication factor (OTP). As a result, an attacker with valid credentials can bypass 2FA by directly accessing authenticated resources without completing OTP verification.

## Steps to Exploit
1. Log in using valid credentials.
2. When redirected to the OTP verification page (`/login2`), do not submit the OTP.
3. Manually navigate to an authenticated endpoint such as `/my-account`.
4. Observe that access is granted despite the second factor not being verified.

## Proof of Concept

### Step 1 - Primary Authentication
```http
POST /login

Valid username and password submitted
```

### Step 2 - OTP Verification
```http
GET /login2

OTP not provided
```

### Step 3 - Direct Access
```http
GET /my-account
```

### Response
```http
HTTP/1.1 200 OK
```

```text
Full account access granted without completing 2FA verification.
```

## Impact
- Complete bypass of two-factor authentication.
- Unauthorized account access using only first-factor credentials.
- Increased risk from credential stuffing, phishing, and password reuse attacks.
- 2FA protection rendered ineffective.

## Remediation
1. Do not create fully authenticated sessions until all authentication factors are verified.
2. Restrict access to authenticated endpoints until 2FA is successfully completed.
3. Implement server-side tracking of authentication state.
4. Automatically redirect partially authenticated users to the OTP verification page.

## CVSS Score
**CVSS v3.1: 8.8 (High)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H`

## CVSS Justification
- **Attack Vector:** Network – exploitable remotely using valid credentials.
- **Attack Complexity:** Low – requires only direct navigation to authenticated endpoints.
- **Privileges Required:** Low – requires valid first-factor credentials.
- **User Interaction:** None.
- **Confidentiality:** High – unauthorized access to sensitive account data.
- **Integrity:** High – attackers can perform actions as the victim.
- **Availability:** High – attackers may disrupt account functionality or data.