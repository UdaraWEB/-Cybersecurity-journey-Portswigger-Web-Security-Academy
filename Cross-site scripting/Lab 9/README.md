# PortSwigger Lab: Reflected DOM XSS

##  How I solved it
1. I went to the website's search box and searched for a test string.
2. The website handles search results via a JSON response and executes it using the unsafe `eval()` function.
3. The application escapes double quotes (`" -> \"`) but forgets to escape backslashes (`\`).
4. To break out of the JSON string context, I used this payload in the search box:
   `\"-alert(1)}//`
5. My injected backslash cancelled out the application's escaping mechanism. This allowed the double quote to close the string, execute `alert(1)`, 
   and solve the lab!

##  Learnings
* **The Danger of `eval()`:** Using `eval()` to parse JSON data is highly insecure. Developers should always use `JSON.parse()` instead.
* **Flawed Escaping:** Only escaping quotes while leaving backslashes (`\`) unescaped allows attackers to bypass security filters by 
    neutralizing the escape character.
