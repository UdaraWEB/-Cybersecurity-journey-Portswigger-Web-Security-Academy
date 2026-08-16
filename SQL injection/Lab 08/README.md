# PortSwigger Web Security Academy - Lab Write-up

## Lab Name
SQL injection UNION attack, retrieving multiple values in a single column

## Difficulty
(Practitioner)

## Objective
The goal of this lab is to exploit a SQL Injection vulnerability to retrieve usernames and passwords from the `users` table. However, the query only returns 2 columns, and only **one** column supports text data. Therefore, multiple values must be retrieved within a single column.

---

## Method of Exploitation (Step-by-Step)

### Step 1: Find the Text-Compatible Column
I verified that the backend query returns **2 columns** and fuzzed them to find which one supports string/text data:
```sql
'+UNION+SELECT+NULL,+'abc'--
```
**Result:** The server returned `200 OK`, proving that **only the 2nd column** can display text data.

---

### Step 2: Use String Concatenation (PostgreSQL syntax)
Since I needed to extract both `username` and `password` but had only one text column available, I used the PostgreSQL string concatenation operator (`||`). I also injected a tilde (`~`) character as a separator to cleanly distinguish usernames from passwords:

```sql
'+UNION+SELECT+NULL,+username||'~'||password+FROM+users--
```
**Result:** The application combined the credentials and displayed them in the 2nd column format on the webpage.

---

### Step 3: Extract Administrator Account Details
I searched the output for `administrator~` and successfully extracted the combined credentials:
* **Combined Output:** `administrator~l3kqx9aq75i7pdpgxh8p`

---

## Lab Resolution
I extracted the password portion, navigated to the **My Account** section, logged in as the `administrator`, and successfully completed the lab.

## Key Takeaways
* Learned how to overcome UI display limitations when the number of target data fields exceeds the available text columns.
* Mastered database-specific string concatenation syntax (using `||` for PostgreSQL).
* Understood how this technique scales in the real world when multiple columns are audited using open-source tools like **SQLMap**.
