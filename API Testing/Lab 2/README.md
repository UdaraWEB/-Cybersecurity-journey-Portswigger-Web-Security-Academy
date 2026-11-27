# PortSwigger Lab: Exploiting server-side parameter pollution in a query string

##  How to Solve

1. **Capture the Password Reset Request:**
   * Go to **My Account > Forgot password?** and submit the username `administrator`.
   * In Burp Suite, locate the `POST /forgot-password` request in **Proxy > HTTP history** and send it to **Repeater**.

2. **Identify Server-Side Parameter Pollution:**
   * In Repeater, test injecting URL-encoded characters into the `username` parameter:
     * `username=administrator%26x=y` (`&`) returns `"Parameter is not supported"`, confirming the input is passed into a backend query string.
     * `username=administrator%23` (`#`) truncates the rest of the backend query and returns `"Field not specified"`, revealing a hidden `field` parameter.

3. **Discover the Hidden Variable Name:**
   * Inspect `/static/js/forgotPassword.js` in **HTTP history** to find how the reset token is handled in the URL (`/forgot-password?reset_token=`).
   * This reveals the internal variable name: `reset_token`.

4. **Retrieve the Administrator's Reset Token:**
   * In Repeater, inject the `field` parameter set to `reset_token` and truncate the remaining backend query:
     ```http
     username=administrator%26field=reset_token%23
     ```
   * Send the request. The server responds with `200 OK` containing the `administrator` password reset token.

5. **Reset Password & Delete Carlos:**
   * Visit `/forgot-password?reset_token=<CAPTURED_TOKEN>` in the browser and set a new password for `administrator`.
   * Log in as `administrator`, navigate to the **Admin panel**, and delete `carlos` to solve the lab.

---

##  What I Learned

* **Server-Side Parameter Pollution (SSPP):** When a frontend server embeds user input directly into a backend API query without proper URL encoding, attackers can inject `&` (`%26`) to add parameters or `#` (`%23`) to truncate existing ones.
* **Error Message Enumeration:** Detailed error messages (e.g., `"Field not specified"`, `"Parameter is not supported"`) act as an oracle, leaking hidden backend parameter names and structure.
* **JavaScript Reconnaissance:** Client-side `.js` files frequently expose internal API endpoints, parameter names, and token structures (`reset_token`) that can be leveraged during parameter fuzzing.
