# PortSwigger Lab 2 ; DOM-based vulnerabilities 

## Lab name
DOM XSS using web messages and a JavaScript URL

## How I Solved 

1. **Identified the Flaw:** Found an `addEventListener('message')` that checks for the presence of "http:" or "https:" using `indexOf()` before passing the message directly to `location.href`.
2. **Crafted the Bypass Payload:** Used the `javascript:` syntax to execute the function and appended `//http:` at the end to satisfy the weak check.
3. **The Payload:**
   ```html
   <iframe src="https://0adf009804f10b4982ca2e2f00860097.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
   ```
4. **Execution:** Hosted this inside the Exploit Server and delivered it to the victim. The target site accepted the message, passed the check, and triggered the `print()` function.

## What I Learned
* **`location.href` Sink:** Setting `location.href` to a `javascript:` pseudo-protocol URL executes JavaScript code in the browser.
* **Flawed String Validation:** Using `indexOf()` to check if a URL contains "http:" or "https:" is insecure. Attackers can bypass this by placing the required string anywhere in the payload, such as a comment (`//http:`).


