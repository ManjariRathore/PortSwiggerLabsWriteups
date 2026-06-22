# Basic SSRF against the local server

## Description
The application is vulnerable to Server-Side Request Forgery (SSRF) through the `stockApi` parameter. The server makes HTTP requests to user-supplied URLs without proper validation, allowing attackers to access internal resources that are not directly exposed to external users.

## Steps to Exploit
1. Navigate to a product page and click **Check Stock**.
2. Intercept the request using Burp Suite.
3. Locate the `stockApi` parameter.
4. Replace the original URL with a localhost address.
5. Forward the request.
6. Observe that the response contains content from the internal admin interface.
7. Send a request targeting administrative functionality through the SSRF endpoint.

## Proof of Concept

### Original Parameter
```text
https://stock.weliketoshop.net/product/stock/check?productId=1&storeId=1
```

### SSRF Payload
```text
stockApi=http://localhost/admin
```

### Administrative Action
```text
stockApi=http://localhost/admin/delete?username=carlos
```

### Result
```text
The application retrieves and returns content from the internal admin interface, allowing access to privileged functionality.
```

## Impact
- Access to internal services not exposed to external users.
- Unauthorized access to administrative functionality.
- Bypass of network-level access controls.
- Potential compromise of internal systems and sensitive data.

## Remediation
1. Validate user-supplied URLs against a strict allowlist of approved domains.
2. Block requests to localhost, loopback addresses, and private IP ranges.
3. Enforce authentication and authorization on internal administrative interfaces.
4. Implement network egress filtering to restrict outbound connections.
5. Monitor and alert on suspicious outbound requests from the application.

## CVSS Score
**CVSS v3.1: 9.8 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## CVSS Justification
- **Attack Vector:** Network – exploitable through manipulation of HTTP parameters.
- **Attack Complexity:** Low – requires only modification of a URL value.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – attackers can access sensitive internal resources.
- **Integrity:** High – attackers can perform privileged administrative actions.
- **Availability:** High – internal services may be disrupted or manipulated.

