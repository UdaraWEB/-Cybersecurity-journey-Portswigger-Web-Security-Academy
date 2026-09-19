# PortSwigger Lab: DOM XSS in document.write

##  What is this lab?
This lab had a DOM-based XSS vulnerability. The website took input directly from the URL (`location.search`) and printed it onto the page using an unsafe method (`document.write`). The input was reflecting inside a `<select>` (drop-down) element.

##  How I solved it
1. I opened a product page and added `&storeId=test` to the URL.
2. I inspected the page source and saw that my input (`test`) was placed inside the `<select>` tag.
3. To break out of the select tag and execute JavaScript, I created this payload:
   `"></select><img src=1 onerror=alert(1)>`
4. I added this payload to the `storeId` parameter in the URL and hit Enter.
5. The payload successfully closed the `<select>` tag and injected an `<img>` tag. Since the image source was invalid (`src=1`), the `onerror` event triggered and executed `alert(1)`, solving the lab!

##  Key Learnings
* Using unsafe sinks like `document.write` to handle URL inputs without filtering is dangerous.
* To exploit XSS, you must inspect the DOM, see where your input lands, and use characters like `"` and `>` to break out of the HTML tags.
