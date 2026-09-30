# PortSwigger Lab Write-up: Reflected XSS with AngularJS sandbox escape without strings

##  How I Solved It

1. I analyzed the application and realized it was parsing inputs via legacy AngularJS expressions, introducing a potential Client-Side Template Injection (CSTI) vector.
2. The core restriction of this environment was twofold: the standard `$eval` function was entirely unavailable, and utilizing any string literals (`'` or `"`) was completely blocked or encoded.
3. To bypass the AngularJS sandbox without using strings, I leveraged **Prototype Modification** to disable the sandbox parser. I rewrote the native `String.prototype.charAt` method by assigning it to the array join method:
   ```javascript
   toString().constructor.prototype.charAt=[].join
   ```
4. Since quote marks were forbidden, I bypassed string filters using `String.fromCharCode()` alongside dynamic ASCII numeric sequences to construct the attack string (`x=alert(1)`):
   ```javascript
   toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)
   ```
5. I chained these techniques inside the AngularJS `orderBy` filter using the array pipeline operator:
   ```text
   ?search=1&toString().constructor.prototype.charAt%3d[].join;|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)=1
   ```
6. When the application parsed the query parameter, the modified prototype crippled the internal security checks, causing the `orderBy` filter to evaluate the dynamic string directly as raw JavaScript, triggering `alert(1)` and solving the lab.

##  How to Find This Bug in the Real World

1. **Identify AngularJS Presence:** Inspect the page source code for structural attributes like `ng-app`, `ng-controller`, or `ng-model`. Alternatively, run `angular` or `angular.version` in the browser console to confirm the framework is running active.
2. **Inject Template Expressions:** Input basic expression templates such as `{{7*7}}` into available search fields, profile forms, or URL parameters. 
3. **Observe Evaluation:** If the output gets evaluated on the frontend and prints **`49`** instead of reflecting the raw text `{{7*7}}`, it confirms the existence of a high-probability Client-Side Template Injection (CSTI) flaw.
4. **Research Version-Specific Escapes:** Check the running version and map it against publicly documented sandbox escape payloads on resources like GitHub or PayloadAllTheThings to finalize your working exploit execution.

##  Key Learnings

* **Client-Side Template Injection (CSTI):** Standard XSS testing often looks for raw HTML tag injection. However, modern bug hunting requires scanning for template syntax (like `{{}}` or framework expressions) which can bypass common server-side sanitizers.
* **Breaking Security Assumptions:** AngularJS sandbox filters were designed under the assumption that core JavaScript components behave predictably. Modifying object prototypes dynamically breaks the core logic of the sandbox parser entirely.
* **WAF/Filter Evasion via Character Codes:** When string constraints prevent typical XSS payloads, utilizing global functions like `String.fromCharCode()` allows testers to feed pure integers that turn into valid executable strings post-filtering, rendering static string checks obsolete.
