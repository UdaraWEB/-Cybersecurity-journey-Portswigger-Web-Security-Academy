# PortSwigger Web Security Academy - Lab Write-up

## Lab Name
SQL injection UNION attack, determining the number of columns returned by the query

## Difficulty
 (Apprentice)

## Objective
The goal of this lab is to determine the exact number of columns returned by a product category filter query by executing a SQL Injection UNION attack.

---

## Method of Exploitation (Step-by-Step)

### Step 1: Intercept the Request
I navigated to the website, clicked on a product category filter, and captured the HTTP request using **Burp Suite Proxy**. Then, I sent the request to **Burp Repeater** (`Ctrl + R`).

### Step 2: Fuzz the Column Count Using NULL
Since the query must return the exact same number of columns as the original query, I incrementally added `NULL` values to the `UNION SELECT` payload to avoid data type mismatch errors:

* **Attempt 1:** `'+UNION+SELECT+NULL-- ` -> Application returned `500 Internal Server Error` (Incorrect column count).
* **Attempt 2:** `'+UNION+SELECT+NULL,+NULL-- ` -> Application returned `500 Internal Server Error` (Incorrect column count).
* **Attempt 3:** `'+UNION+SELECT+NULL,+NULL,+NULL-- ` -> Application returned `200 OK` (Successful injection).

---

## Lab Resolution
The server responded with an HTTP `200 OK` status only when exactly **3 NULL values** were injected. This confirmed that the backend database query returns exactly **3 columns**. The lab was successfully solved.

## Key Takeaways
* Understood the fundamental rule of SQL `UNION` attacks: the injected query must return the exact same number of columns as the original application query.
* Learned how to safely use `NULL` values to determine the column count without triggering data type conflicts.
* Discovered real-world alternatives for larger databases, such as using the `ORDER BY` clause or automated tools like **SQLMap** for faster enumeration.
