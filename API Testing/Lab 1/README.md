# PortSwigger Lab: Exploiting an API endpoint using documentation


##  How to Solve

1. **Login & Capture Traffic:**
   * Open Burp Suite and start the proxy.
   * Log in to the application using the credentials `wiener:peter`.
   * Navigate to **My Account**, update the email address, and capture the request in Burp Suite.

2. **Analyze in Repeater:**
   * Go to **Proxy > HTTP history**, find the `PATCH /api/user/wiener` request, and send it to **Repeater**.

3. **Discover API Documentation (Path Truncation):**
   * In Repeater, remove `/user/wiener` from the request path so it becomes `GET /api/` (or follow the 302 redirect).
   * Send the request. The server responds with `200 OK` containing the interactive API documentation.

4. **Exploit the Endpoint:**
   * The documentation reveals an endpoint to delete users: `DELETE /api/user/{username}`.
   * In Repeater, change the request line to:
     ```http
     DELETE /api/user/carlos HTTP/2
     ```
   * Send the request. The server responds with `200 OK` (`"User deleted"`), solving the lab.

---

##  What I Learned

* **Path Truncation:** Always test removing trailing segments of an API endpoint (e.g., `/api/v1/users/profile` -> `/api/v1/users` -> `/api/`). Developers often leave API documentation (like Swagger/OpenAPI) publicly accessible at the root API path.
* **HTTP Method Tampering:** Even if the frontend only uses `GET` or `PATCH`, the backend API might accept dangerous methods like `DELETE` or `PUT` on the same endpoint.
* **Broken Access Control (BOLA/IDOR):** Never assume an API endpoint validates permissions properly. In this lab, a regular user (`wiener`) was able to execute an admin-level action (`DELETE`) on another user (`carlos`) without proper authorization checks.
