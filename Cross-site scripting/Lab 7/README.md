---

##  Lab 8: Reflected XSS into a JavaScript string with angle brackets HTML encoded
* **What I searched (Payload):**
  ```html
  '-alert(1)-'
  ```
* **What I learned:** 
  Even if a website encodes angle brackets (`<` and `>`), XSS is still possible if our input is reflected inside an inline JavaScript string
  literal (`var input = 'USER_INPUT'`). By injecting a single quote `'`, we can break out of the string context and use a JavaScript
  operator (like `-`) to force the browser to evaluate and execute our malicious `alert(1)` function to keep the syntax valid.
