# PortSwigger Lab: Reflected XSS with some SVG markup allowed

##  How I solved it
1. I observed that the application enforces a strict Web Application Firewall (WAF) that blocks common HTML tags (like `<script>` and `<img>`), returning a `400 Bad Request` status code.
2. To identify alternative entry points, I captured the search request in **Burp Suite** and sent it to **Burp Intruder**.
3. **Fuzzing Tags:** I configured the Intruder to fuzz the tag context using PortSwigger's XSS tag wordlist.
   The attack revealed that standard HTML tags were blocked, but SVG-specific markup like `<svg>` and `<animatetransform>` returned a successful `200 OK`.
4. **Fuzzing Events:** I then fuzzed the allowed `<animatetransform>` tag against a comprehensive list of JavaScript event attributes. The results showed that the WAF whitelisted the `onbegin` animation event attribute (`200 OK`).
5. I combined these discoveries into the final zero-interaction payload and entered it directly into the search bar:
   ```html
   <svg><animatetransform onbegin=alert(1)></svg>
   ```
7. The browser successfully parsed the SVG layout, triggered the `onbegin` animation phase without requiring any user clicks, and solved the lab.

##  Key Learnings for the Real World
* **The SVG XSS Goldmine:** Web Application Firewalls (WAFs) often filter out traditional HTML vectors (`<script>`, `<body>`, `<img>`) but completely overlook graphic-rendering tags like **SVG** or **MathML**. SVG tags support their own native execution events (`onbegin`, `onrepeat`, `onload`) making them highly lethal for bypassing strict blacklists.
* **Automated Fuzzing Strategy:** When auditing target applications with heavy structural filtering, trying payloads manually is highly inefficient. Pushing full wordlists through **Burp Intruder** allows you to accurately map out exactly which characters, tags, or events slip past the active security controls.
* **Zero-Interaction Exploits:** In professional penetration testing and bug bounties, payloads requiring zero user interaction (like `onbegin` or `onload`) carry significantly higher impact and severity rankings because they seamlessly trigger the second the victim loads the malicious link.
