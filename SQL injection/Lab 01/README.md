# PortSwigger Web Security Academy: SQL Injection

## Lab 01: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

###  Lab Objective
Display all products in all categories, including hidden (unreleased) products, by exploiting the product category filter.

* **Difficulty:** Apprentice
* **Vulnerability Type:** SQL Injection (SQLi)

---

###  Vulnerability Analysis
The backend executed this vulnerable SQL query when filtering categories:
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
Because the input isn't sanitized, we can break out of the string using a single quote (`'`) and inject a condition that is always true (`OR 1=1`) to bypass the `released = 1` restriction.

---

###  Step-by-Step Exploitation

1. **Intercept the Request:** Opened Burp Suite, turned on Intercept, and clicked on a product category (e.g., `Gifts`) to capture the HTTP request.
2. **Inject the Payload:** Modified the `category` parameter by adding `'+OR+1=1--` to the end.
   * **Modified Parameter:** `category=Gifts'+OR+1=1--`
3. **Submit & Verify:** Forwarded the request. The database evaluated `1=1` as TRUE and returned all hidden products, successfully solving the lab.

---

###  Final Payload Used
```sql
'+OR+1=1--
```

---

###  Remediation
Use **Parameterized Queries (Prepared Statements)** instead of concatenating user input directly into SQL statements. This ensures input is treated strictly as data, not executable code.

