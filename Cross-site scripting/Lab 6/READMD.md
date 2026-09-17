---

##  Lab 7: Stored XSS into anchor href attribute with double quotes HTML-encoded

###  What I Did (Steps to Solve)
1. Visited a blog post and scrolled down to the comment section.
2. Filled out the comment form, but in the **"Website"** input field, I entered the payload: `javascript:alert(1)`.
3. Posted the comment and went back to the blog post.
4. Clicked on my **Comment Author Name**, which instantly triggered the `alert(1)` function.

###  What I Learned
* **Stored XSS Danger:** Unlike Reflected XSS, Stored XSS saves the malicious payload directly into the website's database.
  This means the attack is permanent and will target *any* user who visits the page and interacts with the injected element.
  
* **Context Over Breakouts:** Even if a website encodes double quotes `"` to prevent attackers from breaking out of HTML attributes,
 security is still bypassed if the input lands directly inside a dangerous sink like an anchor `href`. No breakout is required because
 the `javascript:` schema can execute code natively from within the attribute.
