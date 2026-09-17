

##  Lab 6: Reflected XSS into attribute with angle brackets HTML-encoded
* **What I searched (Payload):**
  ```html
  "onmouseover="alert(1)
  ```
* **What I learned:** 
  Even if a website blocks or encodes angle brackets (`<` and `>`), XSS is still possible if our input lands inside an HTML
  attribute (like `value="..."`). By using a double quote `"`, we can break out of the attribute and inject an event handler like `onmouseover`.
  This executes JavaScript as soon as the victim moves their mouse over the affected element, without needing any HTML tags.
