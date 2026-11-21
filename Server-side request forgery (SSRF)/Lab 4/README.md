# Lab: SSRF with blacklist-based input filter 

## How to Solve

1. Intercept the request when clicking the **"Check stock"** button on any product page using Burp Suite.
2. Send the intercepted `POST /product/stock` request to **Burp Repeater**.
3. Locate the `stockApi` parameter in the request body, which contains a URL pointing to an internal stock check API.
4. Change the `stockApi` parameter value to `http://127.1/%2561dmin` and send the request to access the internal admin interface.
5. Inspect the HTML response to find the endpoint URL for deleting the user `carlos` (`/admin/delete?username=carlos`).
6. Update the `stockApi` parameter to `http://127.1/%2561dmin/delete?username=carlos` and send the request to delete the user and solve the lab.

## What I Learned

* **Trust Boundaries:** Applications often trust requests originating from `localhost` (the server itself) and bypass normal authentication or access controls.
* **Parameter Manipulation:** Any parameter accepting a full URL or hostname (like `stockApi`) is a potential vector for Server-Side Request Forgery (SSRF) if the server fetches it without proper validation.
* **Internal Recon:** By reading the HTML responses returned from internal endpoints, an attacker can map out hidden administrative paths and trigger sensitive actions.
