# PortSwigger Web Security Academy: Visible Error-Based SQL Injection

This repository contains my detailed writeup and proof-of-concept for solving the PortSwigger lab: **"Visible error-based SQL injection"**. 

##  Lab Description
The application uses a tracking cookie (`TrackingId`) for analytics and executes a SQL query containing its value. Unlike completely blind SQL injection vulnerabilities, this application displays the full database error messages directly on the web page (Verbose Errors). 

Our goal is to induce a specific database error that leaks the administrator's password and use it to log in.

---

## 🛠️ Step-by-Step Walkthrough (How I Solved It)

### Step 1: Identifying the SQL Injection & Error Visibility
First, I captured the main page request using **Burp Suite Proxy** and sent it to the **Repeater** tool to observe how the application handles database errors.

1. I injected a single quote `'` into the `TrackingId` cookie:  
   `TrackingId=xyz'`  
    **Result:** The server responded with an **HTTP 500 Internal Server Error**, and noticeably, it displayed the raw database error message text directly in the HTML response.
   
2. I commented out the rest of the query using `--`:  
   `TrackingId=xyz'--`  
    **Result:** The error disappeared, and the server returned an **HTTP 200 OK**. This confirmed a structural SQL injection vulnerability.

---

### Step 2: Overcoming the Cookie Character Limit (Crucial Step)
Web servers often enforce a character limit on cookie values. During my initial tests, appending a long payload to the existing tracking ID caused the query to truncate (cut off) before completion, resulting in a generic 500 error instead of a useful database message.

* **Solution:** I completely deleted the original alphanumeric tracking ID value (`1HGfxtDSFazSgOJf`) to free up character space. 
* I started my payload directly with a single quote `'` so that the custom SQL script could fit entirely within the allowed cookie length limit.

---

### Step 3: Triggering a Data Type Conversion (CAST) Error
To force the database to print the administrator's password, I utilized a **Data Type Conversion Error**. I attempted to force the database to transform a string (Text) value into a numerical integer (`int`).

**Payload Strategy:**
Using the `||` string concatenation operator (compatible with databases like PostgreSQL/Oracle), I injected the following logic:

```http
TrackingId=' || CAST((SELECT password FROM users LIMIT 1) AS int) || '
```

#### 🔍 What happened inside the Database:
1. The subquery `SELECT password FROM users LIMIT 1` fetched the administrator's password from the database table.
2. The `CAST(... AS int)` function tried to convert that alphanumeric password string into a plain number.
3. Because an alphabetical password cannot be represented as an integer, the backend database threw a fatal conversion error.

---

### Step 4: Extracting the Password from the Response
Because the server prints raw database errors directly to the user interface, I inspected the response pane in Burp Repeater and searched for the syntax failure.

####  Successfully Exfiltrated Password:
The error message explicitly leaked the raw data inside the error string:
`ERROR: invalid input syntax for type integer: "d6tmyj37uzzqx1xts4mo"`

By extracting the content inside the quotes, I successfully recovered the complete administrator password:  
**`d6tmyj37uzzqx1xts4mo`**

---

### Step 5: Logging In & Completing the Lab
1. Navigated to the `/login` endpoint of the lab.
2. Entered `administrator` as the username.
3. Supplied the leaked password: `d6tmyj37uzzqx1xts4mo`

 **Result:** Successfully authenticated as the administrator, and the lab was marked as **Solved**!

---

##  Remediation (How to Fix It)
To successfully mitigate Visible Error-Based SQL Injection flaws, developers must apply the following structural changes:
1. **Use Parameterized Queries (Prepared Statements):** This separates user-supplied input data from the SQL code structure entirely, preventing arbitrary SQL execution.
2. **Disable Verbose Error Messages:** Configure the web server and database to never output detailed error syntax or technical debug logs to the end-user. Instead, handle errors gracefully using generic error pages (e.g., standard custom HTTP 500 pages) so that data extraction via error messages becomes impossible.

