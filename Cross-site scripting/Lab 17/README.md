# PortSwigger Lab Write-up: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped

## How I Solved It

1. I submitted a random alphanumeric string into the search input and inspected the page source code to analyze the reflection context.
2. I discovered that the input was reflected inside a JavaScript template literal wrapped in backticks (`` ` ``):
   ```javascript
   var searchResults = `my_input`;
   ```
3. The application heavily sanitized input by Unicode-escaping angle brackets, quotes, backslashes, and backticks, making a standard string breakout impossible.
4. However, because the reflection context was a template string, I leveraged JavaScript's built-in **String Interpolation** mechanic (`${...}`), which executes code directly inside the literal without breaking out of the boundaries.
5. I executed the attack by submitting the following payload:
   ```javascript
   ${alert(1)}
   ```
6. The browser parsed the page, recognized the dynamic template expression, and immediately executed the `alert(1)` function, successfully solving the lab.

## Key Learnings for Bug Bounties

* **Power of Context Over Filtering:** Heavy validation rules that block quotes, tags, or backticks are completely bypassed if the injection context happens to be a template literal. You do not need to escape the string to achieve execution.
* **Modern JavaScript Vectors:** With the massive adoption of modern frameworks and ES6 standards, template literals are everywhere in web development. Testing for `${}` execution is a vital modern bug hunting check.
* **Real-World Hunting Strategy:** If you see user input rendering between backticks, test for arithmetic evaluation first using payloads like `${7*7}`. If the application source shows the raw payload but renders the output as `49` on the frontend, it marks a highly valid XSS finding.
