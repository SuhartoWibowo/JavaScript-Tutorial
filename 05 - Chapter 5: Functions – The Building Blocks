# Chapter 5: Functions – The Building Blocks

As your programs grow in size, writing all your logic in a single, continuous stream of code becomes difficult to manage. If you need to perform the same action in ten different places, copying and pasting the code is inefficient and makes updates tedious.

To solve this, we use **functions**. A function is a reusable block of code designed to perform a specific task. By grouping code into functions, we adhere to the programming principle **DRY** (Don't Repeat Yourself), making our programs modular, easier to read, and simpler to debug.

---

## 5.1 Function Declarations vs. Function Expressions

In JavaScript, there are multiple ways to define a function. The two most common foundational methods are function declarations and function expressions.

### 1. Function Declarations
A function declaration uses the `function` keyword followed by the function's name.

```javascript
// Defining the function
function sayHello() {
    console.log("Hello, welcome back!");
}

// Calling (or invoking) the function
sayHello(); // Outputs: "Hello, welcome back!"
```

One unique behavior of function declarations is that they are **hoisted**. This means JavaScript moves them to the top of their scope during compilation, allowing you to call the function *before* its actual declaration in the file.

```javascript
greetUser(); // This works!

function greetUser() {
    console.log("Welcome!");
}
```

### 2. Function Expressions
A function expression defines a function and assigns it to a variable. The function itself can be anonymous (have no name).

```javascript
const sayGoodbye = function() {
    console.log("Goodbye!");
};

// Calling the function expression
sayGoodbye(); // Outputs: "Goodbye!"
```

Unlike declarations, function expressions are **not hoisted**. If you try to call a function expression before declaring it, you will get an error.

---

## 5.2 Parameters and Arguments

Functions become significantly more useful when they can accept input. 

*   **Parameters:** The placeholders listed in the function's definition.
*   **Arguments:** The actual values passed to the function when it is invoked.

```javascript
// 'name' is a parameter (placeholder)
function personalGreeting(name) {
    console.log(`Hello, ${name}!`);
}

// "Alice" and "Bob" are arguments (actual values)
personalGreeting("Alice"); // Outputs: "Hello, Alice!"
personalGreeting("Bob");   // Outputs: "Hello, Bob!"
```

### Default Parameters
If a function expects an argument but you do not provide one, the parameter defaults to `undefined`. To prevent bugs, you can set default parameter values.

```javascript
function calculateTotal(price, taxRate = 0.05) {
    let total = price + (price * taxRate);
    console.log(`The total is: $${total}`);
}

calculateTotal(100);       // Uses default taxRate of 0.05. Outputs: "The total is: $105"
calculateTotal(100, 0.10); // Overrides default with 0.10. Outputs: "The total is: $110"
```

### Rest Parameters (`...`)
If you don't know how many arguments a user might pass into your function, you can use **rest parameters**. This groups any number of incoming arguments into a single array.

```javascript
function sumAllNumbers(...numbers) {
    let total = 0;
    for (let num of numbers) {
        total += num;
    }
    return total;
}

console.log(sumAllNumbers(1, 2, 3));       // Outputs: 6
console.log(sumAllNumbers(10, 20, 30, 40)); // Outputs: 100
```

---

## 5.3 Return Values and the Execution Context

A function can do more than just print output using `console.log()`. It can also process data and send a value back to the code that called it using the `return` keyword.

```javascript
function square(num) {
    return num * num; 
}

// Capture the return value in a variable
let result = square(4); 
console.log(result); // Outputs: 16
```

### Key Behaviors of `return`:
1.  **Stops Execution:** When JavaScript encounters a `return` statement inside a function, it stops executing that function immediately. Any code written after the `return` statement is ignored.
2.  **Implicit Undefined:** If a function does not have a `return` statement, or if the `return` statement has no value after it, the function returns `undefined` by default.

---

## 5.4 Introduction to Arrow Functions

Introduced in ES6, **arrow functions** provide a highly concise syntax for writing function expressions. They are widely used in modern JavaScript development.

Let's look at how a traditional function expression translates into an arrow function:

```javascript
// Traditional Function Expression
const doubleTraditional = function(number) {
    return number * 2;
};

// Arrow Function Syntax
const doubleArrow = (number) => {
    return number * 2;
};
```

### Implicit Returns
If an arrow function only contains a single expression, we can make it even shorter by removing the curly braces `{}` and the `return` keyword. This is called an **implicit return**.

```javascript
// Single parameter doesn't require parentheses, and returns the result implicitly
const doubleShort = number => number * 2;

console.log(doubleShort(5)); // Outputs: 10
```

*Note on Arrow Functions:* Aside from syntax, arrow functions have a unique behavior regarding the `this` keyword. Unlike standard functions, arrow functions do not create their own `this` context; instead, they inherit it from their surrounding lexical scope. We will examine this in detail in Chapter 9.

---

## Exercises and Review

Practice building and calling functions with these exercises:

1. **The Temperature Converter:** Write a function named `toCelsius` that accepts a Fahrenheit temperature as a parameter and returns the converted temperature in Celsius. Formula: $C = (F - 32) \times \frac{5}{9}$.
2. **The Maximum Finder:** Write an arrow function named `findMax` that accepts three numbers as parameters and returns the largest of the three. Test it with several different arguments.
3. **Array Sum Tool:** Create a function `averageOfAll(...grades)` that uses rest parameters to accept any number of grade values, calculates their average, and returns it. If no grades are passed, it should return `0`.
