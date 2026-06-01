# Chapter 2: Variables, Constants, and Data Types

In the previous chapter, we wrote a simple program that displayed a message on the screen. However, programs need to do more than just output static text—they need to remember data, manipulate it, and make decisions based on it. 

To do this, we use **variables** and **constants**. In this chapter, we will explore how to store data in JavaScript, how the language classifies different kinds of data, and how it handles conversions between those types.

---

## 2.1 Declaring Variables and Constants

Think of a variable as a labeled storage box. You can put data inside the box, label it with a name, and refer to that name whenever you need to retrieve or change the data.

In modern JavaScript, there are three keywords used to declare variables: `let`, `const`, and `var`.

### 1. Reassignable Variables: `let`
Use `let` when you expect the value of the variable to change over time.

```javascript
let age = 25;
console.log(age); // Outputs: 25

// Reassigning a new value to the same variable
age = 26; 
console.log(age); // Outputs: 26
```

### 2. Read-Only Constants: `const`
Use `const` (short for "constant") when the value should not change after it is assigned. If you try to reassign a `const` variable, JavaScript will throw an error.

```javascript
const birthYear = 1998;
console.log(birthYear); // Outputs: 1998

// Attempting to reassign will cause an error
birthYear = 1999; // TypeError: Assignment to constant variable.
```
*Best Practice:* Default to using `const` for all your variables unless you know the value will need to be reassigned later. This makes your code more predictable and prevents accidental changes.

### 3. The Legacy Way: `var`
Before modern JavaScript was introduced in 2015 (ES6), `var` was the only way to declare variables. 

```javascript
var price = 9.99;
```
You will still see `var` in older codebases and tutorials, but it is generally avoided in modern development. Unlike `let` and `const`, which are block-scoped (restricted to the block of code inside `{}` where they are defined), `var` is function-scoped. This behavior can lead to subtle, hard-to-track bugs. 

---

## 2.2 Primitive Data Types

Data types describe the kind of value a variable holds. JavaScript classifies data into two main categories: **Primitives** and **Objects** (Reference types). In this chapter, we will focus on the seven primitive data types. Primitives are immutable, meaning the values themselves cannot be changed (though the variable holding them can be reassigned).

### 1. String (Text)
Strings represent textual data. You can write strings using single quotes (`'`), double quotes (`"`), or backticks (`` ` ``).

```javascript
let firstName = 'Alice';
let lastName = "Smith";

// Template Literals (using backticks) allow you to embed variables directly
let greeting = `Hello, ${firstName} ${lastName}!`; 
console.log(greeting); // Outputs: Hello, Alice Smith!
```

### 2. Number
Unlike other languages that distinguish between integers (whole numbers) and floats (decimals), JavaScript has a single `Number` type for both.

```javascript
let score = 100;      // Integer
let price = 19.99;    // Decimal

// Special Number values:
let notANumber = 0 / 0; // NaN (Not a Number)
let infinityValue = 1 / 0; // Infinity
```

### 3. Boolean
Booleans represent logical entities and can only have one of two values: `true` or `false`. They are essential for controlling the flow of programs.

```javascript
let isLoggedin = true;
let hasSubscription = false;
```

### 4. Null
The `null` type has exactly one value: `null`. It represents an intentional absence of any object value. Think of it as explicitly marking a box as "empty."

```javascript
let currentSession = null; // No active session at this moment
```

### 5. Undefined
The `undefined` type means a variable has been declared but has not yet been assigned a value.

```javascript
let address;
console.log(address); // Outputs: undefined
```

### 6. Symbol
Introduced in ES6, a `Symbol` represents a unique, immutable identifier. They are often used as unique keys in objects.

```javascript
const uniqueID = Symbol("id");
```

### 7. BigInt
Standard numbers in JavaScript cannot safely represent integers larger than $2^{53} - 1$. For extremely large numbers, you append an `n` to the end of an integer to create a `BigInt`.

```javascript
const massiveNumber = 9007199254740991n;
```

---

## 2.3 Dynamic Typing

JavaScript is a **dynamically typed** language. This means you do not have to explicitly declare the type of data a variable will hold, and the same variable can change its data type during program execution.

You can check the current data type of a variable using the `typeof` operator:

```javascript
let variable = "Hello";
console.log(typeof variable); // Outputs: "string"

variable = 42;
console.log(typeof variable); // Outputs: "number"

variable = true;
console.log(typeof variable); // Outputs: "boolean"
```

*Note on `null`:* If you run `typeof null`, it returns `"object"`. This is a well-known, historic bug in JavaScript that cannot be fixed because doing so would break many legacy websites on the internet.

---

## 2.4 Type Conversion vs. Type Coercion

Sometimes you need to work with values of different types. JavaScript handles this in two ways: explicit conversion and implicit coercion.

### Type Conversion (Explicit)
This occurs when you manually convert a value from one type to another.

```javascript
// Converting String to Number
let strNumber = "123";
let parsedNumber = Number(strNumber); // 123 (as a number)

// Converting Number to String
let age = 30;
let ageAsString = String(age); // "30" (as a string)

// Converting to Boolean
console.log(Boolean(1)); // true
console.log(Boolean(0)); // false
```

### Type Coercion (Implicit)
This happens automatically behind the scenes when JavaScript tries to resolve an operation between two different types.

```javascript
// Addition Coercion: JavaScript converts numbers to strings if one operand is a string
let result1 = "5" + 2; 
console.log(result1); // Outputs: "52" (string concatenation)

// Subtraction Coercion: JavaScript converts strings to numbers for other mathematical operations
let result2 = "5" - 2; 
console.log(result2); // Outputs: 3 (numeric subtraction)

let result3 = "five" - 2;
console.log(result3); // Outputs: NaN (since "five" cannot be converted to a number)
```

---

## Exercises and Review

Try working through these exercises to practice using variables and data types:

1. **The Constant Challenge:** Create a constant variable named `myCountry` and assign your country's name to it. Try to reassign it to a different country name on the next line. Run the script and observe the error in your terminal or console.
2. **String Interpolation:** Create three variables: `item`, `quantity`, and `price`. Use template literals to print a sentence like: *"You bought 3 apples for $1.50 each."* using those variables.
3. **Coercion Quiz:** Predict the output and data type of the following expressions, then test them in your console:
   * `"10" + "10"`
   * `"10" - "5"`
   * `true + 1` (Hint: Under the hood, `true` often converts to `1`, and `false` to `0`)
   * `typeof (true + "1")`
