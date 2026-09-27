# PortSwigger Lab Write-up: Reflected XSS into a JavaScript string with single quote and backslash escaped

##  How I Solved It

1. I entered a random alphanumeric string into the search box and inspected the page source code. I observed that my input was reflected inside a
   script block within a JavaScript string variable:
   ```javascript
   var searchTerms = 'my_input';
   ```
3. When trying to break out of the string using a single quote (`'`), the application escaped it by adding a backslash (`\'`), which blocked standard string breakout.
4. Instead of trying to bypass the string escaping, I decided to close the entire script block early by injecting a closing script tag (`</script>`).
5. I crafted and submitted the following payload into the search box:
   ```html
   </script><script>alert(1)</script>
   ```
6. The browser's HTML parser detected the injected `</script>` tag, closed the original script block immediately, and executed my new `<script>` block, triggering
   the `alert(1)` pop-up and solving the lab.

##  Key Learnings 

* **HTML Parser Trumps JS Context:** The browser's HTML parser runs before the JavaScript engine. Even if user input is strictly escaped inside a JavaScript string,
   injecting a raw `</script>` tag will force the browser to terminate the entire script block immediately.
* **Look Beyond String Escaping:** When you notice that single or double quotes are being successfully escaped with backslashes (`\'` or `\"`), do not get stuck
  trying to bypass the filter. Shift your focus to see if you can break out of the outer HTML tag structure entirely.
* **Real-World Hunting Strategy:** During bug hunting, if your input reflects inside a `<script>` tag, always test if the application sanitizes angle brackets
   (`<` and `>`). If it does not, you can easily bypass complex string filtering by simply closing the script context and opening a new one.
