# Chapter 19: Error Handling, Debugging, and Testing

Programming is as much about diagnosing and fixing errors as it is about writing new features. No matter your level of experience, you will write bugs. What separates a junior developer from a professional is the ability to systematically handle, locate, and prevent these errors.

In this chapter, we will examine how JavaScript classifies errors, explore how to use browser-based debugging tools to inspect execution in real time, and learn how to write automated **unit tests** using modern testing frameworks like Jest or Vitest to verify that code functions correctly.

---

## 19.1 Types of Errors

To resolve errors efficiently, we must first classify them. In JavaScript, bugs generally fall into three distinct categories:

### 1. Syntax Errors
These occur during the compilation or parsing phase, before the code even begins to run. If JavaScript encounters a structural violation—such as a missing curly brace or a misspelled keyword—it throws a `SyntaxError` and stops execution immediately.

```javascript
// SyntaxError: Unexpected token '}'
function sayHello() {
    console.log("Hello")
// Missing closing brace
```

### 2. Runtime Errors
These occur while the program is actively executing. The syntax is correct, but the engine is asked to perform an impossible operation—such as referencing a variable that does not exist or calling a non-existent method on `null`.

```javascript
let user = null;
user.greet(); // TypeError: Cannot read properties of null (reading 'greet')
```

### 3. Logical Errors
Logical errors are the most insidious because they throw no errors, warnings, or console logs. The code runs smoothly, but it yields the incorrect result because the developer's underlying logic is flawed.

```javascript
function calculateDiscount(price, discountPercent) {
    // Logical Error: subtraction is performed before division due to missing parentheses
    return price - discountPercent / 100 * price; 
}
```

---

## 19.2 Modern Debugging Techniques

While inserting `console.log()` statements is a common way to inspect variable states, it can clutter code and is inefficient for complex state tracking. Professional developers use built-in browser developer tools to debug code systematically.

### 1. Breakpoints
Instead of guessing, you can instruct the browser to pause execution at a specific line of code.
1. Open your browser's Developer Tools (`F12`) and select the **Sources** (or **Debugger**) tab.
2. Locate and open your JavaScript file.
3. Click on the line number where you want to pause. A blue marker (breakpoint) will appear.
4. Run your application. The browser will pause execution at that exact line, allowing you to hover over variables and inspect their current values in memory.

### 2. Navigation Tools (Step Controls)
When paused at a breakpoint, the debugger provides controls to step through code execution line by line:
*   **Step Over:** Execute the current line and move to the next line. If the current line is a function call, it runs the entire function in the background without jumping inside it.
*   **Step Into:** If the current line is a function call, jump inside that function so you can debug it line by line.
*   **Step Out:** If you are inside a function, execute the remainder of that function and jump back out to the calling scope.

### 3. The `debugger` Keyword
You can also force the browser to pause execution programmatically by placing the `debugger;` statement directly inside your source code. If your browser's Developer Tools are open, execution will pause on this line automatically.

```javascript
function computeInvoice(amount) {
    const taxRate = 0.08;
    debugger; // Browser will pause here automatically if DevTools are open
    return amount + (amount * taxRate);
}
```

---

## 19.3 Introduction to Automated Testing (Jest/Vitest)

Manually clicking through your website to test every button after every single update is time-consuming and error-prone. **Automated testing** solves this by using code to test your code.

In unit testing, we verify that the smallest testable parts of our application (usually individual functions) behave correctly under various conditions. Modern testing libraries (such as **Jest** and **Vitest**) share a common syntax to define tests.

### Designing a Unit Test
Suppose we have a simple file containing a utility function we want to test:

```javascript
// file: math.js
export function add(a, b) {
    return a + b;
}
```

To test this function, we create a matching test file (by convention named with a `.test.js` or `.spec.js` extension) and write assertions using the `describe`, `test` (or `it`), and `expect` blocks.

```javascript
// file: math.test.js
import { describe, test, expect } from "vitest"; // or imported from Jest
import { add } from "./math.js";

// "describe" groups related tests together
describe("Math utilities", () => {
    
    // "test" (or "it") defines a single, isolated test case
    test("should correctly add two positive numbers", () => {
        // "expect" contains the actual code execution
        // "toBe" is an assertion matcher that verifies the expected output
        expect(add(2, 3)).toBe(5);
    });

    test("should handle negative numbers correctly", () => {
        expect(add(-1, -1)).toBe(-2);
    });
});
```

When you run your test suite (via `npm test`), the test runner executes these functions and alerts you if any outputs fail to match your expectations. This allows you to refactor your code with confidence, knowing instantly if you have accidentally introduced a regression.

---

## Exercises and Review

Refining your diagnostic and testing patterns is vital to writing clean code:

1. **The Diagnostic Challenge:** Look at the following runtime error stack trace. Explain in your own words what went wrong, which file the error occurred in, and which line you should look at to fix it:
   ```text
   Uncaught ReferenceError: totalValue is not defined
       at calculateReceipt (invoice.js:14:18)
       at handleCheckout (app.js:42:5)
   ```
2. **The Logic Bug:** Identify and correct the logical error in the following function designed to check if a user is eligible for a senior discount (ages 65 and up):
   ```javascript
   function isEligibleForDiscount(age) {
       if (age > 65) {
           return true;
       }
       return false;
   }
   // Test case: A customer who is exactly 65 years old runs the code.
   ```
3. **Write a Unit Test:** Write a simple function `isEven(num)` that returns `true` if a number is even, and `false` if it is odd. Write a corresponding Vitest/Jest unit test suite using `describe`, `test`, and `expect` that tests both outcomes.
