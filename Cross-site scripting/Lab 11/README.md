# PortSwigger Lab: Reflected XSS into HTML context with most tags and attributes blocked

##  How I solved it
1. I discovered that the website implements a Web Application Firewall (WAF) that blocks common XSS tags (like `<script>`, `<img>`)
   and common attributes (like `onerror`, `onload`), returning a `400 Bad Request` error.
2. By leveraging fuzzing techniques (via Burp Intruder), I identified that the `<body>` tag and the `onresize` event attribute were
   completely whitelisted and bypassed the WAF.
3. Because triggering an `onresize` event manually requires user interaction, I used the PortSwigger Exploit Server to achieve zero-click execution.
4. I embedded the target website inside an `<iframe>` and passed the allowed payload through the search parameter. I used the `onload` event to
    dynamically resize the iframe width, forcing the `onresize` trigger automatically:
   ```html
   <iframe src="https://<YOUR-LAB-ID>.web-security-academy.net/?search=%3Cbody+onresize%3Dprint%28%29%3E" onload="this.style.width='500px'"></iframe>
   ```
8. I delivered the exploit to the victim, which automatically resized the iframe, triggered the `print()` function inside the vulnerable body context, and successfully solved the lab.

##  Key Learnings
* **WAF Deficiencies:** Firewalls and blacklists are often incomplete. If even a single HTML5 tag (like `<body>`) and a rare attribute (like `onresize`)
   are missed by developers, the entire WAF security boundary can be bypassed.
* **Fuzzing with Purpose:** Manually guessing payloads against a strict WAF is inefficient. Automating wordlist checks via fuzzing helps
    pinpoint exact structural holes in real-world application filters.
* **Maximizing Exploit Delivery:** Attackers don't have to rely on victims resizing their browsers or clicking links. Using nested elements
  like an `<iframe>` combined with automated style shifts can forcefully trigger event-driven payloads without user knowledge.
