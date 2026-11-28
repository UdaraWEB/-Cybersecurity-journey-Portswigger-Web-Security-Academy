# Lab: Password reset broken logic

**Vulnerability:** Broken Password Reset Logic

### How to Solve
1. Request a password reset for `wiener` and open the reset link from the email client.
2. Capture the password reset POST request using Burp Suite.
3. Send the request to Repeater and remove the `temp-forgot-password-token` value from both the URL and request body.
4. Change the `username` parameter from `wiener` to `carlos`.
5. Set a new password and send the modified request.
6. Log in as `carlos` using the new password and access the My account page.

### What I Learned
- Password reset token validation.
- Modifying HTTP requests using Burp Repeater.
- Exploiting broken password reset logic to achieve account takeover.
