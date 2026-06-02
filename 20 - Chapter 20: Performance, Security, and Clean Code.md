# Chapter 20: Performance, Security, and Clean Code

Building an application that functions correctly is only the first step in professional software development. To deliver software that succeeds in production, you must ensure it runs efficiently under load, protects users from malicious attacks, and is structured cleanly so that you and your team can maintain it over time.

In this final chapter of our core curriculum, we will look at how JavaScript handles memory behind the scenes, explore performance optimization techniques, investigate fundamental client-side security guidelines, and discuss clean code practices.

---

## 20.1 Memory Management and Garbage Collection

JavaScript uses automatic memory management, which means developers do not have to manually allocate and free memory in their code.

The memory lifecycle generally follows three phases:
1.  **Allocation:** The runtime reserves memory when variables, objects, or functions are declared.
2.  **Usage:** The program reads or writes to the allocated memory.
3.  **Release:** The runtime frees up memory that is no longer needed so it can be reused.

### The Mark-and-Sweep Algorithm
To determine when memory is no longer needed, modern JavaScript engines use the **Mark-and-Sweep** algorithm. The engine starts at the root (the global `window` or `global` object) and "marks" all objects that are reachable from it. It then marks any objects referenced by those objects, and so on.

Once the sweep phase begins, any memory that was not marked (meaning it is completely unreachable from the root) is safely reclaimed (garbage-collected).

```
[ Global Root ] 
       |
       v (reachable)
  [ User Object ] ---> [ Address Object ] (reachable)
  
  [ Orphaned Object ]                     (unreachable -> swept)
```

### Common Memory Leaks
Even with automatic garbage collection, developers can write patterns that trap memory, creating **memory leaks**:

*   **Accidental Global Variables:** If you assign a value to a variable without declaring it using `let` or `const` in non-strict mode, it is attached to the global `window` object and stays in memory forever.
*   **Forgotten Timers:** A `setInterval` callback that references a DOM element inside its block will keep that DOM element in memory, even if the element is removed from the page, unless `clearInterval` is explicitly called.
*   **Out-of-DOM References:** Storing a reference to a DOM element in a JavaScript variable means that even if that element is removed from the DOM tree, its memory cannot be reclaimed because the JavaScript variable still points to it.

---

## 20.2 Performance Optimization: Debouncing and Throttling

Some browser events—like scrolling, resizing the window, dragging elements, or typing into search bars—can fire dozens of times per second. If you attach a heavy computation or an API network request to these high-frequency events, your application will stutter or lag.

To solve this, we use two limiting patterns: **Debouncing** and **Throttling**.

```
EVENT TRIGGER FREQUENCY: | | | | | | | | |

DEBOUNCED OUTPUT:        |_________________| (Fires once, after pause)

THROTTLED OUTPUT:        |____|____|____|___| (Fires once per interval)
```

### 1. Debouncing
Debouncing groups multiple sequential calls into a single execution, running the function only after a set period of inactivity has elapsed.

*Use Case:* An autocomplete search field. Instead of making an API call on every single keystroke, you wait until the user pauses typing for $300$ms.

```javascript
function debounce(func, delay) {
    let timeoutId;
    
    return function(...args) {
        // Clear any active timer to restart the waiting window
        clearTimeout(timeoutId);
        
        timeoutId = setTimeout(() => {
            func.apply(this, args);
        }, delay);
    };
}

const handleSearch = debounce((query) => {
    console.log(`Searching API for: ${query}`);
}, 300);
```

### 2. Throttling
Throttling limits the execution of a function to once every set period of time, regardless of how frequently the event triggers.

*Use Case:* Infinite scrolling pages. You want to check how close the user is to the bottom of the page, but checking every pixel is inefficient. Throttling checks the scroll position at most once every $100$ms.

---

## 20.3 Client-Side Security: Preventing Cross-Site Scripting (XSS)

The most common security vulnerability in frontend applications is **Cross-Site Scripting (XSS)**. XSS occurs when an attacker successfully injects malicious JavaScript into a trusted website, executing it in a victim's browser.

If malicious code executes, it can read sensitive data inside `localStorage` or send requests on behalf of the logged-in user.

### Preventing XSS
1.  **Sanitize User Input:** Never trust data supplied by a user. If you display user-provided text, always use `textContent` instead of `innerHTML`. The former treats text strictly as character data and will not execute nested `<script>` tags.
2.  **Use Sanitization Libraries:** If your application *must* render user-formatted HTML (for example, in a rich text editor), pass the HTML through a trusted sanitization library like **DOMPurify** to strip out malicious script nodes and inline execution attributes before inserting it into the DOM.
3.  **Implement Content Security Policy (CSP):** A CSP is an HTTP header returned by the server that instructs the browser exactly which origins and domains are permitted to execute scripts on your page.

---

## 20.4 Clean Code Principles

Your code is read far more often than it is written. Writing code cleanly ensures that your application remains readable, structured, and easy to refactor.

### 1. Self-Documenting Naming Conventions
Avoid cryptic abbreviations or vague variable names. Use descriptive nouns for variables and active verbs for functions.

```javascript
// Avoid
const d = 10;
function calc(x) { ... }

// Prefer
const daysUntilExpiration = 10;
function calculateSalesTax(price) { ... }
```

### 2. The Single Responsibility Principle (SRP)
A function should do one thing, do it cleanly, and do it well. If your function is validation, math calculation, and rendering a DOM update inside the same block, it should be split into smaller, modular functions.

### 3. Avoid Magic Numbers and Strings
A "magic number" is a hardcoded value in your code that has no apparent explanation. Instead, assign these values to named constants at the top of your files or in config files to give them clear semantic meaning.

```javascript
// Avoid
if (user.role === 3) {
    displayAdminPanel();
}

// Prefer
const ROLE_ADMIN = 3;

if (user.role === ROLE_ADMIN) {
    displayAdminPanel();
}
```

---

## Exercises and Review

Put optimization, security, and cleanliness principles into practice:

1. **The Leak Hunt:** Inspect the following function. Identify the memory leak and modify the code to prevent it:
   ```javascript
   function trackScroll() {
       let element = document.querySelector("#banner");
       
       window.addEventListener("scroll", () => {
           if (element) {
               console.log("Scrolled past banner");
           }
       });
       
       // Element is eventually removed from page
       element.remove();
   }
   ```
2. **Implement a Throttle Function:** Write a simple `throttle(func, limit)` function. The function should ensure that the wrapped `func` runs at most once every `limit` milliseconds, dropping any extra calls made during that window.
3. **Refactor Challenge:** Refactor the following code to adhere to clean code principles (descriptive naming, avoiding magic numbers, and single responsibility):
   ```javascript
   function p(a, s) {
       let tax = a * 0.08;
       let t = a + tax;
       if (s === "CA") {
           t += 5;
       }
       document.querySelector("#out").textContent = t;
   }
   ```
