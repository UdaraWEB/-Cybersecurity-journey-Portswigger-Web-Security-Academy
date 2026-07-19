# PortSwigger Web Security Academy: SQL Injection

## Lab 02: SQL injection vulnerability allowing login bypass

### Lab Objective
Log in to the application as the `administrator` user by exploiting a SQL injection vulnerability in the login function.

* **Difficulty:** Apprentice
* **Vulnerability Type:** SQL Injection (SQLi) - Login Bypass

---

### Vulnerability Analysis
The backend executed this vulnerable SQL query during login:
```sql
SELECT * FROM users WHERE username = 'input_user' AND password = 'input_password'
```
Because the input isn't sanitized, we can inject a single quote (`'`) to close the username string early and use `--` to comment out the rest of the query, bypassing the password check entirely.

---

### Step-by-Step Exploitation

1. **Intercept the Request:** Opened Burp Suite, turned on Intercept, and submitted a login request with a random password.
2. **Inject the Payload:** Modified the `username` parameter to `administrator'--` to target the admin account.
   * **Modified Parameter:** `username=administrator'--`
3. **Submit & Verify:** Forwarded the request. The query became `WHERE username = 'administrator'--...`, which successfully logged into the administrator account without a password.

---

### Final Payload Used
```sql
administrator'--
```

---

### Remediation
Use **Parameterized Queries (Prepared Statements)** to separate user data from the SQL code structure. This stops inputs from changing the query logic.
