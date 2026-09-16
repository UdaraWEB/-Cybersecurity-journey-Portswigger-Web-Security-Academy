---

##  Lab 5: DOM XSS in jQuery selector sink using a hashchange event

###  What I Did (Steps to Solve)
1. Opened the vulnerable target blog website.
2. Copied the main lab URL from the browser's address bar.
3. Went to PortSwigger's **Exploit Server** to simulate an attacker-controlled website.
4. Pasted an automated `<iframe>` exploit code inside the **Body** section, injecting the target URL and an XSS payload into the URL hash (`#`):
   ```html
   <iframe src="https://web-security-academy.net" onload="this.src+='<img src=x onerror=print()>'"></iframe>
   ```
5. Clicked **Store** and then **Deliver exploit to victim**, successfully triggering the browser's `print()` function on the victim's end.

###  What I Learned
* **jQuery `$()` Vulnerability:** Older versions of jQuery automatically convert text into executable HTML elements if a malicious payload is passed directly into the `$()` selector function.
* **The `hashchange` Event:** Websites often track changes made to the URL hash (anything after the `#` symbol). Attackers can exploit this event to dynamically feed malicious code into the page without reloading it.
* **Attacking via iframes:** In the real world, hackers do not need the victim to type a payload. By embedding the vulnerable site inside an `<iframe>` on their own malicious website, attackers can automatically force a URL hash change and trigger the exploit invisibly in the background when a user visits their link.
