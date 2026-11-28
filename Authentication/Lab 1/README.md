# Lab: 2FA Simple Bypass 

### How to Solve
1. Login using `wiener:peter`.
2. Get the 2FA verification code from the Email Client.
3. Complete login and visit `/my-account`.
4. Logout and login using `carlos:montoya`.
5. When prompted for the 2FA code, manually navigate to `/my-account`.
6. Carlos's account page loads, and the lab is solved.

### What I Learned
- 2FA can be bypassed if the application does not properly enforce verification.
- Sensitive pages must verify the user's authentication status before granting access.
- Direct URL navigation can expose broken authentication and access control vulnerabilities.
