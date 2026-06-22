# High-level logic vulnerability

## Description
The application fails to validate product quantities when items are added to the shopping cart. By submitting negative quantity values, an attacker can reduce the cart total and purchase high-value items at a significantly reduced cost.

## Steps to Exploit
1. Add a high-value item to the shopping cart.
2. Add a second lower-priced item to the cart.
3. Intercept the second add-to-cart request using Burp Suite.
4. Modify the `quantity` parameter to a negative value (e.g., `-100`).
5. Forward the request.
6. Observe that the cart total decreases based on the negative quantity.
7. Complete the purchase at the manipulated price.

## Proof of Concept

### Normal Request
```http
POST /cart

productId=2&quantity=1
```

### Modified Request
```http
POST /cart

productId=2&quantity=-100
```

### Result
```text
The cart total is reduced by the value of 100 units of the second product, allowing expensive items to be purchased at a heavily discounted price.
```

## Impact
- Unauthorized reduction of order totals.
- Purchase of expensive products at near-zero cost.
- Financial losses due to manipulation of business logic.
- Lack of proper server-side validation of cart data.

## Remediation
1. Validate that product quantities are positive integers greater than zero.
2. Enforce server-side minimum and maximum quantity limits.
3. Recalculate cart totals on the server using trusted values.
4. Implement business rule validation to reject negative or invalid cart states.
5. Log and monitor suspicious quantity values and pricing anomalies.

## CVSS Score
**CVSS v3.1: 6.5 (Medium)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through manipulation of HTTP requests.
- **Attack Complexity:** Low – requires only modification of a quantity parameter.
- **Privileges Required:** Low – requires an authenticated user session.
- **User Interaction:** None.
- **Confidentiality:** None – no sensitive information is disclosed.
- **Integrity:** High – attackers can manipulate order values and business transactions.
- **Availability:** None – no direct impact on service availability.

