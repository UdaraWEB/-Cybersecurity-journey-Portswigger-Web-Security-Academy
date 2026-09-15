# PortSwigger Web Security Academy -  XSS Writeups LAB 2/3/4

##  Lab 1: Reflected XSS into HTML context with nothing encoded
* **What I searched (Payload):**
  ```html
  <script>alert(1)</script>
  ```
* **What I learned:** 
  When a website reflects search inputs back onto the page without any validation, we can replace standard text with `<script>` tags to trick the browser into executing our own JavaScript code.

---

##  Lab 2: DOM XSS in `document.write` sink using source `location.search`
* **What I searched (Payload):**
  ```html
  "><svg onload=alert(1)>
  ```
* **What I learned:** 
  If our payload gets trapped inside an HTML attribute (like `img src`), we must use `"` and `>` to break out of the existing tag. I also learned that XSS doesn't always require `<script>` tags; alternative tags like `<svg>` with the `onload` event handler work perfectly.

---

##  Lab 3: DOM XSS in `document.write` sink inside an image source target
* **What I searched (Payload):**
  ```html
  <img src=1 onerror=alert(1)>
  ```
* **What I learned:** 
  We can trigger JavaScript by intentionally causing a browser error. By injecting a completely new `<img>` tag with an invalid image path (`src=1`), we force the browser to fire the `onerror` event handler, which executes our script immediately without needing standard `<script>` tags.
