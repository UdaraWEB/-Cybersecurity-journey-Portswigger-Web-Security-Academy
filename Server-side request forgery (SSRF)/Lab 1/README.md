# Lab: Basic SSRF against the local server

## How to Solve

1. Tried accessing `/admin` directly, but access was denied.
2. Visited a product page and clicked **Check stock**.
3. Intercepted the stock request using Burp Suite.
4. Sent the request to **Burp Repeater**.
5. Changed the `stockApi` parameter to:

   ```text
   http://localhost/admin
   ```

6. Sent the request and viewed the Admin Panel HTML in the response.
7. Identified the user deletion URL:

   ```text
   http://localhost/admin/delete?username=carlos
   ```

8. Replaced the `stockApi` value with the deletion URL and sent the request.
9. The Carlos user account was deleted, and the lab was successfully solved.

## What I Learned

- Understood the basics of Server-Side Request Forgery (SSRF).
- Learned how to use Burp Suite Proxy and Repeater.
- Learned how to identify and manipulate URL parameters.
- Understood how SSRF can be used to access internal server resources.
- Learned why improper URL validation can lead to SSRF vulnerabilities.
