# PortSwigger Web Security Academy - Lab Write-up

## Lab Name
SQL injection UNION attack, retrieving data from other tables

## Difficulty
(Practitioner)

## Objective
The goal of this lab is to exploit a SQL Injection vulnerability in the product category filter using a `UNION` attack to retrieve the usernames and passwords from a separate table named `users`, and log in as the `administrator`.

---

## Method of Exploitation (Step-by-Step)

### Step 1: Determine Column Count and Data Types
First, I intercepted a category filter request using **Burp Suite** and sent it to **Repeater**. 
To verify the column count and check if they support text (string) data, I injected the following payload:
```sql
'+UNION+SELECT+'abc',+'def'-- 
```
**Result:** The server responded with an HTTP `200 OK`, confirming that the backend query returns **2 columns** and both accept **string data**.

---

### Step 2: Retrieve Credentials from the Users Table
The lab description provided the target table name (`users`) and column names (`username`, `password`). 

Since both columns support text, I crafted a `UNION SELECT` query to fetch data directly from the target columns:
```sql
'+UNION+SELECT+username,+password+FROM+users-- 
```
**Result:** The application executed the payload and appended the contents of the `users` table onto the webpage. 

---

### Step 3: Extract the Administrator Password
I searched the HTTP response for the keyword **`administrator`** and successfully located the associated credentials:
* **Username:** `administrator`
* **Password:** *84ubaod8loev1pxv5dkl*

---

## Lab Resolution
I copied the extracted administrator password, navigated to the **My Account** login page, and authenticated successfully. The lab status updated to solved.

## Key Takeaways
* Mastered the core concept of cross-table data extraction (Data Exfiltration) using SQL `UNION` operators.
* Understood how an attacker can leverage a vulnerability in a public feature (like a product catalog) to compromise restricted database tables (like user credentials).
* Learned the real-world significance of this flaw, which is reported as a **High/Critical severity vulnerability** in Bug Bounty programs.
