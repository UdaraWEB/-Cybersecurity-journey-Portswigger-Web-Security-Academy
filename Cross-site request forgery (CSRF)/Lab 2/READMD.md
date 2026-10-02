# PortSwigger Lab: CSRF where token validation depends on request method

##  How I Solved It

1. **Intercepted & Analyzed:** Captured the email update request (`POST /my-account/change-email`) which contained a `csrf` token parameter.
2. **Method Switching:** Sent the request to Burp Repeater, right-clicked, and selected **"Change request method"** to convert it into a `GET` request.
3. **Tested Bypass:** Removed the `csrf` token parameter from the `GET` request and sent it. The server successfully processed the request without validation.
4. **Crafted & Delivered PoC:** Created a malicious HTML form using the `GET` method without any token and delivered it to the victim via the exploit server:

```html
<form action="https://<YOUR-LAB-ID>.web-security-academy.net/my-account/change-email" method="GET">
    <input type="hidden" name="email" value="attacker@flawed-auth.com" />
</form>
<script>
    document.forms[0].submit();
</script>
```


##  What I Learned

* **Method Flaws:** Developers sometimes enforce security controls (like CSRF token validation) strictly on `POST` requests while neglecting `GET` requests.
* **Bypass Strategy:** Changing the HTTP request method from `POST` to `GET` can completely bypass flawed CSRF token validations if the backend application accepts parameters via both methods.

