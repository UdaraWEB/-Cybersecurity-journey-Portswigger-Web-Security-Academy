# PortSwigger Web Security Academy - Lab Write-up

## Lab Name
Blind SQL injection with conditional responses

## Difficulty
 (Practitioner)

## Objective
The goal of this lab is to exploit a Blind SQL Injection vulnerability within the tracking cookie (`TrackingId`) to exfiltrate the 20-character password of the `administrator` user by analyzing conditional HTML responses ("Welcome back!").

---

## Method of Exploitation (Step-by-Step)

### Step 1: Confirm Blind SQL Injection Vulnerability
I intercepted the laboratory homepage request using **Burp Suite Proxy** and analyzed the behavior of the application by injecting boolean conditions into the `TrackingId` cookie value:

* **True Condition:** `TrackingId=xyz' AND '1'='1` -> Response contained the string **`Welcome back!`**.
* **False Condition:** `TrackingId=xyz' AND '1'='2` -> The **`Welcome back!`** string disappeared from the response.

This conditional behavior confirmed a vulnerable entry point for **Blind SQL Injection**.

---

### Step 2: Determine Password Length
Next, I utilized the `LENGTH()` database function to probe the exact length of the administrator's password:
```sql
TrackingId=xyz' AND (SELECT LENGTH(password) FROM users WHERE username='administrator')=20-- 
```
**Result:** The application evaluated this condition as True and returned **`Welcome back!`**, confirming the target password is exactly **20 characters** long.

---

### Step 3: Automate Character Enumeration via Burp Intruder
To extract the actual password character-by-character without manual overhead, I sent the request to **Burp Intruder** and configured a **Cluster Bomb attack**:

1. **Positions Configuration:** 
   I injected the payload structure targeting the `SUBSTRING` index position and the character guess variable:
   ```sql
   TrackingId=xyz' AND (SELECT SUBSTRING(password,§1§,1) FROM users WHERE username='administrator')='§a§'-- 
   ```
2. **Payload Set 1 (Index Positions):** Type set to `Numbers` from `1` to `20` with a step value of `1`.
3. **Payload Set 2 (Character Character Space):** Type set to `Simple list` containing a custom wordlist of alphanumeric characters (`a-z`, `0-9`).

---

### Step 4: Extract Content and Analyze Differences
I executed the automated brute-force attack (totaling **720 requests**). Instead of viewing full responses, I evaluated the boolean output differences based on the HTTP response lengths or custom grep-match rules. 

A uniquely modified response length indicated a True evaluation statement, successfully revealing each index character sequentially.

* **Extracted Administrator Password:** `enuzomxhbna7icscfxle`

---

## Lab Resolution
I extracted the full 20-character credential string, navigated to the **My Account** section of the website, authenticated as the `administrator`, and successfully completed the challenge.

## Key Takeaways
* Mastered the concept of **Blind SQL Injection** where backend database values are completely hidden from error blocks or visible interface grids.
* Learned how to extract full records by sending logical True/False validation questions to a database server.
* Gained intermediate proficiency with **Burp Intruder** using a multi-variable **Cluster Bomb attack type** to systematically automate character-by-character validation sequences.
