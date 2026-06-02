# Chapter 8: Scope, Hoisting, and Closures

Up to this point, we have focused on writing working code using variables, functions, and objects. To write highly reliable applications, however, we need to understand how JavaScript works under the hood. 

In this chapter, we will explore how JavaScript manages the visibility and accessibility of variables (known as **scope**), how it processes code before executing it (known as **hoisting**), and how functions can retain access to variables from their parent scope even after that parent scope has completed execution (known as **closures**).

---

## 8.1 Understanding Scope

**Scope** refers to the accessibility of variables, functions, and objects in some particular part of your code during runtime. In other words, scope determines where in your code a variable is visible and can be used.

JavaScript has three primary levels of scope:

### 1. Global Scope
A variable declared outside any function or block is in the **global scope**. It can be accessed and modified from anywhere in your code.

```javascript
const globalName = "Alice";

function testScope() {
    console.log(globalName); // Accessible inside functions
}

testScope(); // Outputs: "Alice"
```

### 2. Function (Local) Scope
Variables declared inside a function are local to that function. They cannot be accessed from outside the function.

```javascript
function localTest() {
    let localVar = "Secret";
    console.log(localVar); // Accessible inside
}

localTest();
console.log(localVar); // ReferenceError: localVar is not defined
```

### 3. Block Scope
Introduced with ES6, variables declared with `let` and `const` inside any curly braces `{}` (such as an `if` statement, `switch` block, or `for` loop) are restricted to that block.

```javascript
if (true) {
    let blockVar = "Inside Block";
    var nonBlockVar = "Var is function-scoped";
}

console.log(nonBlockVar); // Outputs: "Var is function-scoped" (var ignores block scope!)
console.log(blockVar);    // ReferenceError: blockVar is not defined
```
As shown above, `var` does not respect block scope, which is one of the main reasons modern JavaScript developers prefer `let` and `const`.

---

## 8.2 Lexical Scope and the Scope Chain

JavaScript uses **lexical scope** (sometimes called static scope). This means that variable scope is determined by the physical placement of variables and nested blocks in the written source code during compilation, rather than where the function is called during runtime.

### The Scope Chain
When JavaScript tries to resolve a variable, it starts looking at the innermost current scope. If it cannot find the variable there, it moves one level outward to the parent scope. This movement continues upward until it either finds the variable or reaches the global scope. If the variable is not found in the global scope, it throws a `ReferenceError`.

```javascript
const userRole = "Admin"; // Global Scope

function parentFunction() {
    const parentVar = "Parent Data"; // Parent Scope
    
    function childFunction() {
        const childVar = "Child Data"; // Child Scope
        
        // Resolving variables through the scope chain
        console.log(childVar);   // Found in current scope
        console.log(parentVar);  // Found in parent scope
        console.log(userRole);   // Found in global scope
    }
    
    childFunction();
}

parentFunction();
```

---

## 8.3 Hoisting

During the execution phase, you might expect that you can only use variables or functions *after* you have written them. However, due to a behavior called **hoisting**, this is not always the case.

**Hoisting** is the JavaScript engine's behavior of conceptually moving declarations to the top of their containing scope before executing the code. Note that only the *declarations* are hoisted, not the *assignments*.

### 1. Function Hoisting
Function declarations are fully hoisted. You can call them before they are declared in the code.

```javascript
greet(); // Outputs: "Hello!"

function greet() {
    console.log("Hello!");
}
```

### 2. Variable Hoisting with `var`
Variables declared with `var` are hoisted to the top of their scope and initialized with the value `undefined`.

```javascript
console.log(testVar); // Outputs: undefined (doesn't crash!)
var testVar = "Stored Data";
console.log(testVar); // Outputs: "Stored Data"
```
Under the hood, the engine interprets the code like this:
```javascript
var testVar; // Hoisted declaration
console.log(testVar); 
testVar = "Stored Data"; // Assignment remains in place
console.log(testVar);
```

### 3. Variable Hoisting with `let` and `const`
`let` and `const` variables are also hoisted, but they are **not initialized**. They exist in an uninitialized state called the **Temporal Dead Zone (TDZ)** from the start of their scope block until the code line that declares them is evaluated.

```javascript
console.log(secureVar); // ReferenceError: Cannot access 'secureVar' before initialization
let secureVar = "Protected Data";
```

---

## 8.4 Closures

A **closure** is created when a nested inner function retains access to the scope (variables) of its outer parent function, even after the parent function has finished executing and returned.

Typically, when a function finishes running, all of its local variables are cleaned up and removed from computer memory. However, closures prevent these variables from being garbage-collected as long as the inner function is still referencing them.

### A Practical Closure Example
Let's build a counter function that securely maintains its own state:

```javascript
function createCounter() {
    let count = 0; // Private state variable
    
    return function() {
        count++; // Accesses parent variable
        return count;
    };
}

const counter1 = createCounter();

console.log(counter1()); // Outputs: 1
console.log(counter1()); // Outputs: 2
console.log(counter1()); // Outputs: 3
```

### Why is this useful?
1.  **Data Privacy:** In the example above, there is no way for external code to directly modify the `count` variable. It can only be changed by invoking the returned inner function. This is a powerful pattern for creating secure, self-contained modules.
2.  **Factory Functions:** You can use closures to generate specialized functions.

```javascript
function makeMultiplier(multiplier) {
    return function(num) {
        return num * multiplier;
    };
}

const double = makeMultiplier(2);
const triple = makeMultiplier(3);

console.log(double(10)); // Outputs: 20
console.log(triple(10)); // Outputs: 30
```

---

## Exercises and Review

Solidify your understanding of scope, hoisting, and closures with these exercises:

1. **Scope Debugger:** Look at the following code and predict what will be printed to the console (or if it will throw an error):
   ```javascript
   let x = 10;
   function outer() {
       let y = 20;
       if (true) {
           let z = 30;
           var w = 40;
       }
       console.log(x);
       console.log(y);
       console.log(w);
       console.log(z);
   }
   outer();
   ```
2. **TDZ Investigation:** Explain in your own words what the Temporal Dead Zone (TDZ) is, and why the following code throws an error even though `let` is theoretically hoisted:
   ```javascript
   console.log(myFood);
   let myFood = "Pizza";
   ```
3. **The Secret Bank Account:** Write a function called `createBankAccount(initialBalance)` that returns an object containing two methods: `deposit(amount)` and `getBalance()`. Use closures to ensure that the actual balance variable cannot be accessed or changed directly from outside the object, only through those two methods.
