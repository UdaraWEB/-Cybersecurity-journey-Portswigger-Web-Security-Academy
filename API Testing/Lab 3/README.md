# PortSwigger Lab: Exploiting server-side parameter pollution in a REST URL

##  How to Solve

1. **Capture the Request:**
   * Trigger a password reset for `administrator` under **My Account > Forgot password?**.
   * Send the `POST /forgot-password` request from **Proxy > HTTP history** to **Repeater**.

2. **Find the API Definition via Path Traversal:**
   * Test injecting `#` (`%23`) and path traversal sequences (`../`) into the `username` parameter.
   * Send `username=../../../../openapi.json%23` to leak the internal API structure: `/api/internal/v1/users/{username}/field/{field}`.

3. **Discover the Reset Token Field:**
   * Check `/static/js/forgotPassword.js` in **HTTP history** to find the reset parameter name: `passwordResetToken`.

4. **Downgrade API Version & Steal Token:**
   * Since the default newer API version blocks `passwordResetToken`, traverse back to the older `v1` endpoint in Repeater:
     ```http
     username=../../v1/users/administrator/field/passwordResetToken%23
     ```
   * Send the request and copy the returned `administrator` password reset token.

5. **Reset Password & Delete Carlos:**
   * Visit `/forgot-password?passwordResetToken=g7n6aul5rm354evnkoldcff8pnylrodt` in the browser and set a new password.
   * Log in as `administrator`, open the **Admin panel**, and delete `carlos`.

---

##  What I Learned

* **REST Path Pollution:** User input embedded directly into a backend REST URL path can be manipulated using path traversal (`../`) and URL fragments (`%23` / `#`) to reach unintended internal endpoints.
* **Improper Inventory Management (API Versioning):** Newer API versions (`v2`, `v3`) often patch security flaws, while older versions (`v1`, `v0`, `beta`) are left active on the server without the same security controls.
* **Leaking API Definitions:** Traversing to root paths with common filenames like `openapi.json` or `swagger.json` can expose the exact structure and hidden parameters of internal APIs.
