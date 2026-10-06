# PortSwigger DOM-based vulnerabilities Lab 1

## Lab Name
DOM XSS using web messages

## How I Solved 

1. **Inspected the Target:** Found an `addEventListener('message')` in the JavaScript source code that blindly takes the message content and inserts it into a `div` container.
2. **Crafted the Exploit:** Hosted a malicious payload inside the PortSwigger Exploit Server using an `iframe` and `postMessage()`.
3. **The Payload:**
   ```html
   <iframe src="https://0a6600320366b28f82667e0600c20056.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')"></iframe>
   ```
4. **Execution:** Delivered the exploit to the victim. The `iframe` loaded the target site, pushed the malicious `<img>` tag into the DOM via `postMessage()`, and triggered the `print()` function because the image source was invalid (`src=x`).


## What I Learned

* **Web Messages (`postMessage`):** A mechanism that allows different windows/domains to communicate safely.
* **The Vulnerability:** If a website sets up a message event listener (`window.addEventListener('message', ...)`) but fails to verify the sender's identity (`event.origin`), anyone can send malicious data to it.
* **Dangerous Sink:** Taking untrusted message data and inserting it directly into the DOM (e.g., using `innerHTML`) creates an easy path to XSS.

