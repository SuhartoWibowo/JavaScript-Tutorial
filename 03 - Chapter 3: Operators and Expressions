# Chapter 3: Operators and Expressions

Now that we know how to store data using variables, the next step is learning how to manipulate that data. In programming, we perform actions on values using **operators**, and we combine variables, values, and operators to form **expressions**.

In this chapter, we will look at how to perform mathematical operations, compare values, evaluate logical conditions, and use modern, clean shorthand operators introduced in recent ECMAScript specifications.

---

## 3.1 Arithmetic and Assignment Operators

### Arithmetic Operators
These operators perform standard mathematical operations on numbers.

| Operator | Name | Example | Result (if `x = 10`, `y = 3`) |
| :--- | :--- | :--- | :--- |
| `+` | Addition | `x + y` | `13` |
| `-` | Subtraction | `x - y` | `7` |
| `*` | Multiplication | `x * y` | `30` |
| `/` | Division | `x / y` | `3.3333...` |
| `%` | Remainder (Modulo) | `x % y` | `1` (The remainder of 10 divided by 3) |
| `**` | Exponentiation | `x ** y` | `1000` ($10^3$) |

We also have increment (`++`) and decrement (`--`) operators to increase or decrease a number by $1$.

```javascript
let counter = 5;
counter++; // Equivalent to counter = counter + 1
console.log(counter); // Outputs: 6

counter--; // Equivalent to counter = counter - 1
console.log(counter); // Outputs: 5
```

### Assignment Operators
Assignment operators assign values to variables. While the basic assignment operator is `=`, you can combine it with arithmetic operators as a shorthand.

```javascript
let score = 10;

score += 5; // Equivalent to: score = score + 5  (score is now 15)
score -= 3; // Equivalent to: score = score - 3  (score is now 12)
score *= 2; // Equivalent to: score = score * 2  (score is now 24)
score /= 4; // Equivalent to: score = score / 4  (score is now 6)
```

---

## 3.2 Comparison Operators and Equality

Comparison operators compare two values and return a Boolean value (`true` or `false`).

*   `>` (Greater than) and `<` (Less than)
*   `>=` (Greater than or equal to) and `<=` (Less than or equal to)

### Loose Equality (`==`) vs. Strict Equality (`===`)
JavaScript handles equality checks in two distinct ways, which is one of the most common sources of bugs for beginners.

#### 1. Loose Equality (`==`)
The loose equality operator checks if two values are equal **after performing type coercion**. If the types are different, JavaScript will attempt to convert one of them to match the other.

```javascript
console.log(5 == "5"); // Outputs: true (The string "5" is coerced to number 5)
console.log(1 == true);  // Outputs: true (The boolean true is coerced to number 1)
```

#### 2. Strict Equality (`===`)
The strict equality operator checks both the **value** and the **data type**. If the data types are different, it immediately returns `false` without trying to convert them.

```javascript
console.log(5 === "5"); // Outputs: false (Number vs. String)
console.log(1 === true);  // Outputs: false (Number vs. Boolean)
```

The same rules apply to inequality checks:
*   Loose Inequality: `!=` (performs coercion)
*   Strict Inequality: `!==` (compares value and type)

*Best Practice:* Always default to using strict equality (`===` and `!==`). It makes code more predictable and prevents unexpected coercion errors.

---

## 3.3 Logical Operators

Logical operators are used to combine multiple Boolean values or conditions. They are vital for controlling decision-making logic in your applications.

### 1. Logical AND (`&&`)
The `&&` operator returns `true` only if **both** operands are true. If the first operand evaluates to `false`, the overall expression is false, and JavaScript stops checking the second operand (this is called *short-circuit evaluation*).

```javascript
const hasDriverLicense = true;
const isSober = true;

const canDrive = hasDriverLicense && isSober;
console.log(canDrive); // Outputs: true
```

### 2. Logical OR (`||`)
The `||` operator returns `true` if **at least one** of the operands is true. It will also short-circuit (stop evaluating) if the first operand is `true`.

```javascript
const hasCash = false;
const hasCreditCard = true;

const canPay = hasCash || hasCreditCard;
console.log(canPay); // Outputs: true
```

### 3. Logical NOT (`!`)
The `!` operator inverts a Boolean value. It turns `true` to `false`, and `false` to `true`.

```javascript
const isWeekend = false;
console.log(!isWeekend); // Outputs: true
```

---

## 3.4 Modern Operators: Nullish Coalescing and Optional Chaining

As JavaScript has evolved, new operators have been added to handle common programming patterns with less code.

### Nullish Coalescing Operator (`??`)
Sometimes you want to assign a fallback/default value to a variable if a preferred value is missing. Previously, developers used the Logical OR (`||`) operator for this:

```javascript
let userScore = 0;
let finalScore = userScore || 10; 
console.log(finalScore); // Outputs: 10
```
In the example above, `0` is a valid score, but because `0` is treated as a "falsy" value in JavaScript, the `||` operator skipped it and assigned the default value `10`.

To fix this, the **Nullish Coalescing operator (`??`)** was introduced. It only uses the fallback value if the left side is explicitly `null` or `undefined`.

```javascript
let userScore = 0;
let finalScore = userScore ?? 10; 
console.log(finalScore); // Outputs: 0 (since 0 is not null or undefined)

let missingScore; // undefined
let defaultScore = missingScore ?? 10;
console.log(defaultScore); // Outputs: 10
```

### Optional Chaining Operator (`?.`)
In JavaScript, trying to read a property from a variable that is `null` or `undefined` will crash your application with an error.

The **Optional Chaining operator (`?.`)** allows you to read deeply nested values safely. If any reference along the path is `null` or `undefined`, the expression short-circuits and simply returns `undefined` instead of throwing an error.

```javascript
// A simple object representation (we will explore objects in depth in Chapter 7)
const user = {
    profile: {
        name: "Alice"
    }
};

// Reading property safely
console.log(user.profile?.name); // Outputs: "Alice"
console.log(user.address?.street); // Outputs: undefined (doesn't crash, even though 'address' is missing)
```

---

## Exercises and Review

Put your knowledge of operators to the test with these exercises:

1. **Modulo Master:** Write a code snippet that uses the remainder operator (`%`) to determine if a number is even or odd. (Hint: Even numbers have a remainder of `0` when divided by `2`).
2. **Predict the Output:** Without running the code, predict what these comparisons will output, then run them to check your answers:
   ```javascript
   console.log(10 !== "10");
   console.log(true && (5 > 3));
   console.log(null ?? "Default Value");
   console.log(0 ?? "Fallback Value");
   ```
3. **The Logical Dilemma:** Write a small program using variables representing standard student requirements: `hasCompletedAssignments` (boolean) and `examScore` (number). A student passes if they have completed their assignments **and** scored higher than `60` on the exam. Print the result using logical operators.
