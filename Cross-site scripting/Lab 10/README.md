# PortSwigger Lab: Stored DOM XSS

##  How I solved it
1. I went to the blog section and located the comment submission form.
2. The application attempts to filter out HTML tags using the JavaScript `replace()` function to encode angle brackets (`<` and `>`).
3. However, `replace()` only modifies the **first instance** it encounters in a string and leaves the rest untouched.
4. To bypass this weak filter, I prepaid an extra pair of empty brackets (`<>`) to occupy the filter and added my XSS payload right after it:
   `<><img src=1 onerror=alert(1)>`
5. I posted the comment. The filter encoded the initial `<>` but allowed the rest of the `<img>` tag to be stored and executed perfectly, triggering `alert(1)` and solving the lab.

##  Key Learnings
* **Flawed Regex/String Replacement:** Relying on the standard JavaScript `replace()` method for sanitization is highly insecure because it does not replace globally. Developers should use `replaceAll()` or a properly configured global Regex (`/g`).
* **Source Code/Behavior Testing:** Instead of guessing blindly in the real world, researchers inspect client-side JavaScript or test input boundaries using redundant characters (like `<<<<>>>>`) to see how the filter reacts before executing the actual attack.
