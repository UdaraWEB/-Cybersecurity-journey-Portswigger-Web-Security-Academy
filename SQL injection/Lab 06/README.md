# PortSwigger Web Security Academy - Lab Write-up

## Lab Name
SQL injection UNION attack, finding a column containing text

## Difficulty
(Practitioner)

## Objective
The goal of this lab is to find which column accepts text (string) data types within a 3-column database query, and use it to retrieve a specific random string provided by the lab.

---

## Method of Exploitation (Step-by-Step)

### Step 1: Confirm Column Count
First, I verified that the query returns exactly **3 columns** by using the following `NULL` injection payload in the category filter:
```sql
'+UNION+SELECT+NULL,+NULL,+NULL--
```
**Result:** The application responded with an HTTP `200 OK`, confirming a 3-column structure.

---

### Step 2: Fuzz for the Text (String) Column
To find which column accepts text data without throwing a database error, I replaced each `NULL` value one by one with a sample string `'abc'`:

* **Testing Column 1:** `'+UNION+SELECT+'aSSNEd',+NULL,+NULL--` -> Returned `500 Internal Server Error` (Column 1 is not a text type).
* **Testing Column 2:** `'+UNION+SELECT+NULL,+'aSSNEd',+NULL--` -> Returned `200 OK` (Column 2 successfully accepts text data!).

---

### Step 3: Solve the Lab with the Target String
The lab required the database to retrieve a specific random string. By looking at the HTML response hint, I found my target string: **`a8RN8d`**. 

I replaced the sample `'abc'` text inside the correct 2nd column position with this target string:
```sql
Pets'+UNION+SELECT+NULL,+'a8RN8d',+NULL--
```

---

## Lab Resolution
After sending the final payload, the server responded with an HTTP `200 OK`. I refreshed the laboratory homepage, and the status updated to successfully solved.

## Key Takeaways
* Learned that a SQL `UNION` attack requires injected data types to match the original query columns.
* Mastered the technique of systematically probing individual columns using a sample string to discover text-compatible display areas.
* Understood how this logic applies to real-world applications where data is exfiltrated by placing data-gathering functions (like `version()`) into identified text columns.
