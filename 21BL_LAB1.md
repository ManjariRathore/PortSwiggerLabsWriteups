# Excessive trust in client-side controls

## Description
The application trusts the product price supplied by the client when adding items to the shopping cart. By modifying the `price` parameter in the add-to-cart request, an attacker can purchase products at arbitrary prices, resulting in unauthorized discounts and financial loss.

## Steps to Exploit
1. Navigate to the page of a high-value product.
2. Click **Add to Cart** and intercept the request using Burp Suite.
3. Locate the `price` parameter in the request body.
4. Modify the price to a lower value (e.g., `1`).
5. Forward the modified request.
6. Proceed to checkout and complete the purchase.
7. Observe that the product is purchased at the manipulated price.

## Proof of Concept

### Original Request
```http
POST /cart

productId=1&quantity=1&price=133700
```

### Modified Request
```http
POST /cart

productId=1&quantity=1&price=1
```

### Result
```text
The product is added to the cart for $0.01 instead of $1337.00.
```

## Impact
- Unauthorized purchase of products at arbitrary prices.
- Financial losses due to manipulated transactions.
- Compromise of pricing integrity and business logic.
- Potential for large-scale abuse and fraud.

## Remediation
1. Never trust client-supplied pricing information.
2. Retrieve product prices from a trusted server-side source.
3. Validate cart contents and pricing before processing payments.
4. Implement integrity checks to detect price manipulation attempts.
5. Log and alert on discrepancies between submitted and actual product prices.

## CVSS Score
**CVSS v3.1: 7.5 (High)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:H/A:N`

## CVSS Justification
- **Attack Vector:** Network – exploitable through HTTP request manipulation.
- **Attack Complexity:** Low – requires modification of a single parameter.
- **Privileges Required:** None.
- **User Interaction:** None.
- **Confidentiality:** None – no sensitive information is disclosed.
- **Integrity:** High – attackers can manipulate product pricing and transaction data.
- **Availability:** None – no direct impact on service availability.

