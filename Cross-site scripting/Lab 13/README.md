# PortSwigger Lab: Reflected XSS into canonical link tag

## 🛠️ How I solved it
1. I discovered that the target website dynamically reflects URL query parameters inside the `<link rel="canonical" href="...">` tag located within the HTML `<head>`.
2. While the application successfully blocked or filtered standard angle brackets (`<` and `>`), it failed to sanitize or URL-encode single quotes (`'`).
3. To exploit this without creating a new HTML tag, I utilized **Attribute Injection**. I crafted a payload to break out of the `href` attribute string boundary
4.  using a single quote, then injected a custom keyboard shortcut handler (`accesskey`) and an execution handler (`onclick`):
   ```text
   ?'accesskey='x'onclick='alert(1)
   ```
5. I appended this payload directly to the lab URL and loaded it in the browser:
   `https://<MY-LAB-ID>.web-security-academy.net/?'accesskey='x'onclick='alert(1)`  (LAB ID = 0a420023035274a58012033e00a100d9)
6. Once loaded, the simulated user triggered the keyboard shortcut (`ALT + X` or `ALT + SHIFT + X`). This forced the invisible canonical link element to get clicked,
7. firing the `onclick` routine to execute `alert(1)` and solving the lab.

## 💡 Key Learnings for Bug Bounties
* **XSS Beyond Angle Brackets:** You do not always need `<` or `>` to achieve Cross-Site Scripting. If your input reflects inside an existing HTML tag attribute,
    breaking the quote boundary (`'` or `"`) allows you to inject entirely new executable attributes into the element.
* **Exploiting Hidden Elements:** Even if a target tag is completely invisible to a normal user (like `<link>`, `<meta>`, or `<input type="hidden">`), you can
    chain browser-specific structural mechanics like `accesskey` properties to forcefully interact with the element.
* **Real-World Hunting Strategy:** During an active Bug Bounty assessment, you should inspect the source code for canonical link tags,
    append a plain single quote (`?'test`) to the URL, and look for raw reflection. If the quote reflects unescaped, it indicates a high-probability vector for a
    functional exploit report.
