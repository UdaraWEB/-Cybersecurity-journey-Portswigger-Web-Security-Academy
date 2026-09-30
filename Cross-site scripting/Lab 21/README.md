# PortSwigger Lab Write-up: Reflected XSS with AngularJS sandbox escape and CSP

##  How I Solved It

1. I analyzed the target application and discovered two heavy security layers: a strict **Content Security Policy (CSP)** restricting inline scripts, and an AngularJS architecture running an active security sandbox.
2. Because the vulnerability was a Reflected XSS, executing it required an external delivery mechanism. I utilized the lab's **Exploit Server** to host a malicious delivery script that forces the victim's browser to redirect to the vulnerable parameter:
   ```html
   <script>
   location='https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cinput%20id=x%20ng-focus=$event.composedPath()|orderBy:%27(z=alert)(document.cookie)%27%3E#x';
   </script>
   ```
3. **Bypassing the CSP:** The implemented CSP blocked classical `<script>` injections. However, it failed to restrict client-side templates. I injected a raw HTML `<input>` tag carrying dynamic AngularJS attributes, completely blinding the CSP filter.
4. **Escaping the Sandbox:** The sandbox strictly isolated window controls. To break out, I leveraged `$event.composedPath()`, a framework routine that returns an array of objects representing the event path leading back up to the global window scope. I chained this vector through an `orderBy` expression filter to execute the un-sandboxed macro `(z=alert)(document.cookie)`.
5. **Enforcing Zero-Click Execution:** To trigger the exploit instantly upon load without human interaction, I added `id=x` and the `ng-focus` directive to the input tag, appending `#x` to the final URL anchor. The browser automatically focused on the element immediately on page load, executing the XSS payload, capturing the session cookies, and solving the expert lab.

## Key Learnings
* **CSP is Not a Silver Bullet:** Developers frequently assume a robust Content Security Policy stops all XSS. If client-side template engines are poorly configured alongside user reflection points, attackers can turn structural HTML tags into functional script triggers.
* **Leveraging Framework Internals:** When framework engines block variables like `window` or `document`, look for overlooked built-in event capabilities (like `$event`, `$selectors`, or scope trails) that expose native browser pathways under the hood.
* **Weaponizing Reflected Vectors:** In real-world bounty operations, a raw reflected link isn't enough to prove true high-severity risk. Chaining it with a malicious cross-origin redirection script (simulated via the exploit server) to achieve automated execution shows high impact, guaranteeing a higher bounty payout.
