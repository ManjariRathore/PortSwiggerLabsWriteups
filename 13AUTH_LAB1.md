# Username enumeration via different responses

## Description
The login functionality returns different error messages for invalid usernames and valid usernames with incorrect passwords. This behavior allows attackers to identify valid user accounts through automated requests, facilitating targeted brute-force and password-spraying attacks.

## Steps to Exploit
1. Navigate to the login page.
2. Submit a login attempt using a random username and any password.
3. Observe the response indicating an invalid username.
4. Submit a login attempt using a valid username and an incorrect password.
5. Observe the different response indicating an incorrect password.
6. Use Burp Intruder with a username wordlist.
7. Identify valid usernames by filtering responses that return the password-related error message.

## Proof of Concept

### Invalid Username Response
```text
Invalid username
```

### Valid Username Response
```text
Incorrect password
```

### Burp Intruder Request
```http
POST /login

username=[FUZZ]&password=password
```

## Impact
- Disclosure of valid usernames.
- Reduced complexity of brute-force and password-spraying attacks.
- Increased risk of account compromise.
- Privacy concerns through confirmation of account existence.

## Remediation
1. Return a generic error message for all failed authentication attempts (e.g., "Invalid credentials").
2. Implement account lockout or rate-limiting mechanisms.
3. Normalize authentication response times.
4. Deploy CAPTCHA or additional verification after repeated failed attempts.

## CVSS Score
**CVSS v3.1: 5.3 (Medium)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through automated HTTP requests.
- **Attack Complexity:** Low – requires only username enumeration attempts.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** Low – valid usernames can be identified.
- **Integrity:** None – no unauthorized modification of data.
- **Availability:** None – no impact on service availability.