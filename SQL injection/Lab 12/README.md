# PortSwigger Lab: Blind SQL Injection with Time Delays

##  Project Overview
This repository contains my solution and write-up for the **"Blind SQL injection with time delays"** lab from PortSwigger's Web Security Academy. This lab demonstrates how to detect hidden SQL injection vulnerabilities when the application does not return any data or errors in the response.

##  What I Learned (Key Concepts)
* **Blind SQL Injection (Time-Based):** Learned how to infer vulnerabilities and test database behavior when the web application suppresses visible database errors or query outputs.
* **Database Time Delays:** Understood how to trigger explicit delays using database-specific functions (in this case, PostgreSQL's `pg_sleep()`).
* **Request Interception:** Gained practical experience using **Burp Suite** to capture, modify, and analyze HTTP requests (specifically targeting tracking cookies).
* **Real-World Impact:** Realized that attackers use these time delays to perform data exfiltration (e.g., extracting admin passwords letter by letter) by asking conditional true/false questions.

---

##  Walkthrough & Solution

### 1. Analysis
The application uses a tracking cookie (`TrackingId`) for analytics and processes its value inside a synchronous SQL query. Since the application behaves identically whether the query returns rows or errors, standard SQLi detection fails. However, we can inject a time-delay payload to verify the vulnerability.

### 2. Exploitation Steps
1. Opened the lab and intercepted the home page request using **Burp Suite Proxy**.
2. Located the `TrackingId` cookie inside the HTTP request headers.
3. Modified the `TrackingId` value by appending a PostgreSQL sleep command:
   ```sql
   TrackingId=x'||pg_sleep(10)--
   ```
4. Forwarded the modified request.
5. Observed that the application took exactly **10 seconds** to respond, confirming that the injected SQL payload successfully executed on the backend database.

**Result:** Lab Successfully Solved! 

---

##  Remediation (How to Fix)
To prevent time-based blind SQL injection, the application should:
* Use **Parameterized Queries (Prepared Statements)** instead of dynamic string concatenation for database queries.
* Implement strict input validation on all user-supplied data, including cookies and headers.
