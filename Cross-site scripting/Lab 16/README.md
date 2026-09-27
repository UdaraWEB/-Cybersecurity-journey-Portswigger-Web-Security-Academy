# PortSwigger Lab Write-up: Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped

## How I Solved It

1. I posted a comment containing a random alphanumeric string in the "Website" input field and inspected how it was rendered in the application's response.
2. I observed that the input was reflected directly inside an JavaScript execution context, specifically within an `onclick` event handler attribute:
   ```html
   <a href="#" onclick="trackClick('my_input');">Author Name</a>
   ```
3. The application heavily sanitized input by escaping single quotes (`'`) and backslashes (`\`), which blocked traditional JavaScript string breakouts.
4. However, I utilized the browser's native HTML parsing behavior. HTML parsers automatically decode HTML entities inside tag attributes before the JavaScript engine executes the code.
5. To exploit this, I used the HTML entity for a single quote (`&apos;`) inside the payload:
   ```text
   http://foo?&apos;-alert(1)-&apos;
   ```
6. When the comment was rendered, the server stored the entity safely. However, when a user interacts with the element, the browser decodes `&apos;` into a literal single quote (`'`), breaking out of the string boundary and successfully executing `alert(1)`.

##  Key Learnings for Bug Bounties

* **Attribute Decoding Order:** Browsers decode HTML entities within HTML attributes (like `onclick`, `href`, `onmouseover`) *before* processing them as JavaScript. This means text filters that only scan for raw symbols like `'` or `"` can be completely bypassed using entities.
* **Context Confusion:** Developers often make the mistake of using HTML encoding to protect data that eventually lands inside a JavaScript context. HTML encoding only neutralizes HTML tag injection, not JavaScript context injection within an event handler.
* **Real-World Hunting Strategy:** Look for input fields that map to user URLs or profile links (like Twitter handles or personal websites). If they map to an `onclick` or `href` attribute, test if submitting HTML entities bypassing the character restrictions allows execution.
