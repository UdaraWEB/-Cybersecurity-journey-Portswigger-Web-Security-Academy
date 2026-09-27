# PortSwigger Lab Write-up: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped

##  How I Solved It

1. I entered a random alphanumeric string into the search box and inspected the source code to identify where my input was being reflected.
2. I observed that the input was placed directly inside a JavaScript string variable:
   ```javascript
   var searchTerms = 'my_input';
   ```
3. To test the protection mechanisms, I submitted a single quote (`'`). The application escaped it by adding a backslash (`\'`) to prevent breaking out of the string context. 
4. However, I noticed that the application failed to escape raw backslashes (`\`) themselves. 
5. To exploit this flaw, I injected a backslash right before a single quote followed by my JavaScript execution code:
   ```text
   \'-alert(1)//
   ```
6. When the server processed this input, it automatically added an extra backslash before my single quote, turning the output in the source code into:
   ```javascript
   var searchTerms = '\\'-alert(1)//';
   ```
7. The browser's interpreter evaluated the first backslash as an escape character for the second backslash (`\\`). This left my single quote (`'`) free to successfully close the string.
   The minus operator (`-`) then connected the expression to fire `alert(1)`, while the trailing comments (`//`) discarded the leftover syntax to solve the lab.

##  Key Learnings

* **The Danger of Incomplete Escaping:** When developers use backslashes to escape user input (like converting `'` to `\'`), they must also escape any backslashes the user inputs (converting `\` to `\\`). Leaving the backslash unescaped completely neutralizes the quote filter.
* **XSS Context Adaptation:** When input reflects inside a JavaScript block where standard HTML characters (`<`, `>`, `"`) are strictly HTML-encoded, you do not need to create new HTML tags. Look to leverage existing script syntax behaviors to trigger the flaw.
* **Real-World Hunting Strategy:** During active reconnaissance, if an input vector reflects inside a script variable, immediately submit `\'` and `\\'` payloads. Observe the behavior in the source viewer—if you see the filter stacking backslashes up without neutralizing your outer boundaries, a high-impact XSS vulnerability exists.
