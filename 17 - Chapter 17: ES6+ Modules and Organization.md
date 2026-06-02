# Chapter 17: ES6+ Modules and Organization

As software applications grow in complexity, keeping all your JavaScript in a single, massive file becomes unmanageable. To keep code maintainable, testable, and scalable, we must split our programs into smaller, self-contained files. This approach is called **modular programming**.

Historically, JavaScript lacked a built-in modularity system, leading to developers relying on third-party formats (like CommonJS in Node.js or AMD in browsers) and polluting the global scope. Today, modern JavaScript features a native, standardized system called **ES Modules (ESM)**. 

In this chapter, we will explore why we need modules, how to share code across files using named and default exports, and how to load modules directly inside a browser.

---

## 17.1 The Necessity of Modular Programming

Before native modules existed, browsers treated every JavaScript file as a flat script executed in the global scope. For example, if you loaded two scripts in an HTML file:

```html
<script src="calculator.js"></script>
<script src="app.js"></script>
```

Any global variable or function declared inside `calculator.js` was automatically shared with `app.js`. If both files accidentally defined a variable with the same name (like `const total = 0`), they would overwrite each other, causing silent and hard-to-track bugs. This issue is known as **global scope pollution**.

An ES Module solves this by acting as a self-contained ecosystem. By default:
*   Every variable, function, or class declared inside a module is **private** to that file.
*   Nothing is exposed to other files unless you explicitly decide to **export** it.
*   To use something from another file, you must explicitly **import** it.

---

## 17.2 Named Exports and Imports

The first way to share code is by using **Named Exports**. This method allows you to export multiple variables, functions, or classes from a single module.

### Creating Named Exports
You can export items individually by placing the `export` keyword directly before their declarations:

```javascript
// file: mathUtils.js

export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}
```

Alternatively, you can write your file normally and export your items as a single block at the bottom of the file:

```javascript
// file: mathUtils.js
const PI = 3.14159;
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

// Exporting as a single block
export { PI, add, subtract };
```

### Consuming Named Imports
To import named items into another file, use the `import` keyword with curly braces `{}`:

```javascript
// file: app.js
import { PI, add } from "./mathUtils.js";

console.log(PI);        // Outputs: 3.14159
console.log(add(5, 10)); // Outputs: 15
```
*Note on file extensions:* When writing standard ES Modules in the browser, you must include the file extension (like `.js`) in your import path.

### Aliasing Imports (`as`)
If you import an item whose name conflicts with an existing variable in your file, you can rename it during the import process using the `as` keyword:

```javascript
// file: app.js
import { add as sum } from "./mathUtils.js";

const add = "This is a conflicting string variable";
console.log(sum(5, 10)); // Outputs: 15 (runs the imported function)
```

### Namespace Imports (`* as`)
If a module exports many different items, you can import them all collectively under a single namespace object:

```javascript
// file: app.js
import * as MathOps from "./mathUtils.js";

console.log(MathOps.PI);         // Outputs: 3.14159
console.log(MathOps.subtract(9, 4)); // Outputs: 5
```

---

## 17.3 Default Exports and Imports

While named exports are perfect for utility files containing many small functions, **Default Exports** are used when a module has a single main focus—such as a single class or a primary configuration object.

Each module can have **at most one** default export.

### Creating a Default Export
To mark an item as a default export, use the `export default` keywords:

```javascript
// file: User.js
export default class User {
    constructor(username) {
        this.username = username;
    }

    display() {
        console.log(`User: ${this.username}`);
    }
}
```

### Consuming a Default Import
When importing a default export, you **do not** use curly braces. Furthermore, because it is the only default item coming from that file, you can assign it any name you want when you import it:

```javascript
// file: app.js
import UserAccount from "./User.js"; // No curly braces, customized name

const activeUser = new UserAccount("dev_alice");
activeUser.display(); // Outputs: "User: dev_alice"
```

You can also combine both named and default imports from a single file if the source module uses both:
```javascript
import User, { someNamedVariable } from "./User.js";
```

---

## 17.4 Loading Modules in the Browser

By default, web browsers treat JavaScript files as legacy scripts. To instruct the browser that a script is an ES Module and should support `import` and `export` statements, you must include the `type="module"` attribute on the `<script>` tag in your HTML file:

```html
<!-- file: index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ES Modules in Action</title>
</head>
<body>
    <h1>Check your console logs</h1>
    
    <!-- type="module" enables ESM features -->
    <script type="module" src="app.js"></script>
</body>
</html>
```

### Key Differences of `type="module"` Scripts
Adding `type="module"` changes how the browser treats and runs your JavaScript code:

1.  **Implicit Strict Mode:** Modules run in strict mode (`"use strict"`) automatically. You do not need to declare it at the top of your files.
2.  **No Global Pollution:** Variables declared at the top level of a module are scoped to the module itself. They are not added to the global browser `window` object.
3.  **Deferred Execution:** Module scripts are deferred by default. The browser will download the module and all of its nested imports in parallel with HTML parsing, but will only execute the code *after* the HTML document is fully parsed and structured. You no longer need to place your scripts at the bottom of the `<body>` to prevent rendering delays.
4.  **CORS Security:** Browsers enforce strict security rules (CORS) for ES Modules. To load and run modules locally on your computer, you must run them through a local development server (like VS Code's "Live Server" extension) rather than simply double-clicking the `index.html` file to open it.

---

## Exercises and Review

Refine your modular architecture skills with these exercises:

1. **The Converter Module:** Create a file named `temperatureConverter.js`. 
   * Write and export two named functions: `celsiusToFahrenheit(c)` and `fahrenheitToCelsius(f)`.
   * Create an `app.js` file, import both functions, and use them to log converted temperatures.
2. **The Default Logger:** Create a file named `Logger.js`.
   * Create a class called `Logger` with a method `log(message)` that prints a formatted timestamp and the message to the console.
   * Export the class as a `default` export.
   * Import the class into another file using a customized import name and instantiate it.
3. **Namespace Consolidation:** Imagine you have three utility files containing geometry formulas: `circle.js`, `square.js`, and `triangle.js`. Write a script that imports them all using the namespace syntax (`import * as Geometry from ...`) to verify how namespaces group code under a single container.
