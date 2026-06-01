# Chapter 4: Control Flow and Loops

By default, JavaScript executes code line by line, from top to bottom. However, real-world applications rarely run in a straight, uninterrupted path. They must adapt to different situations—such as checking if a user is logged in, displaying a message only when an error occurs, or repeating an action multiple times.

This ability to direct the execution path of a program is called **control flow**. In this chapter, we will learn how to make decisions using conditional statements and how to repeat tasks efficiently using loops.

---

## 4.1 Conditional Statements

Conditional statements let your code perform different actions based on different conditions.

### The `if`, `else if`, and `else` Statements
The most common way to direct control flow is with the `if` statement. If a condition evaluates to `true`, the code block inside the curly braces `{}` executes.

```javascript
let temperature = 28;

if (temperature > 30) {
    console.log("It's a hot day outside.");
} else if (temperature >= 18) {
    console.log("The weather is pleasant.");
} else {
    console.log("It's cold outside.");
}
// Outputs: "The weather is pleasant."
```

In this block:
*   The `if` condition checks if `temperature > 30`. Since it is not, JavaScript skips to the next condition.
*   The `else if` checks if `temperature >= 18`. Since $28$ is greater than or equal to $18$, this block executes.
*   The final `else` block acts as a fallback and only runs if none of the preceding conditions are met.

### The Ternary Operator (`? :`)
For simple true/false decisions, writing out a full `if-else` statement can feel unnecessarily long. The **ternary operator** acts as a concise shorthand.

**Syntax:** `condition ? expressionIfTrue : expressionIfFalse`

```javascript
let userAge = 20;

// Traditional if-else
let statusCheck;
if (userAge >= 18) {
    statusCheck = "Adult";
} else {
    statusCheck = "Minor";
}

// Equivalent ternary operator shorthand
let statusCheckTernary = (userAge >= 18) ? "Adult" : "Minor";

console.log(statusCheckTernary); // Outputs: "Adult"
```

---

## 4.2 The `switch` Statement

When you need to compare a single variable against many potential values, writing several `else if` blocks can make your code hard to read. The `switch` statement provides a cleaner alternative.

```javascript
let selectedColor = "red";

switch (selectedColor) {
    case "blue":
        console.log("You selected Blue.");
        break;
    case "red":
        console.log("You selected Red.");
        break;
    case "green":
        console.log("You selected Green.");
        break;
    default:
        console.log("Color not recognized.");
}
// Outputs: "You selected Red."
```

### Key Elements of `switch`:
*   **`case`:** Represents a value to check against the input variable.
*   **`break`:** Exits the `switch` block once a match is found. If you forget to write `break`, the program will continue executing subsequent cases (even if they don't match) until it hits a `break` or reaches the end of the statement. This is known as *fall-through*.
*   **`default`:** Runs if none of the cases match. It acts similarly to the final `else` statement in an `if-else` chain.

---

## 4.3 Loops

Loops are control flow structures that allow you to execute a block of code multiple times. This is incredibly useful for processing items in a list, counting, or repeating tasks.

### 1. The `for` Loop
The `for` loop is ideal when you know in advance how many times you want to run the code.

**Syntax:**
```javascript
for (initialization; condition; increment/decrement) {
    // Code to run
}
```

```javascript
for (let i = 1; i <= 5; i++) {
    console.log(`Iteration number: ${i}`);
}
```
**How it works:**
1.  **Initialization (`let i = 1`):** Executes once before the loop begins. It declares a counter variable, conventionally named `i`.
2.  **Condition (`i <= 5`):** Evaluates before every iteration. If true, the loop body runs. If false, the loop stops.
3.  **Increment (`i++`):** Executes after the loop body runs, modifying the counter variable before checking the condition again.

### 2. The `while` Loop
The `while` loop runs as long as a specified condition remains `true`. It is best used when you do not know exactly how many times the loop will run beforehand.

```javascript
let diceRoll = 0;

while (diceRoll !== 6) {
    // Math.random() generates a decimal between 0 and 1
    // Math.floor() rounds it down to create whole numbers from 1 to 6
    diceRoll = Math.floor(Math.random() * 6) + 1;
    console.log(`Rolled a: ${diceRoll}`);
}
```
*Caution:* Always ensure your loop's condition will eventually evaluate to `false`. Otherwise, you will create an **infinite loop**, which can freeze your browser or crash your program.

### 3. The `do...while` Loop
The `do...while` loop is very similar to the `while` loop, with one key difference: it executes the code block **at least once** before evaluating the condition.

```javascript
let count = 10;

do {
    console.log(`This runs even though count is not less than 5.`);
    count++;
} while (count < 5);
```

---

## 4.4 Loop Control: `break` and `continue`

Sometimes you need to alter the behavior of a loop from inside the loop body. You can do this using `break` and `continue`.

### The `break` Statement
`break` stops the loop immediately and jumps to the code following the loop.

```javascript
for (let i = 1; i <= 10; i++) {
    if (i === 6) {
        break; // Stop the loop entirely when i is 6
    }
    console.log(i); 
}
// Outputs: 1, 2, 3, 4, 5
```

### The `continue` Statement
`continue` skips the rest of the current iteration and jumps directly to the loop's next iteration.

```javascript
for (let i = 1; i <= 5; i++) {
    if (i === 3) {
        continue; // Skip the rest of the code for iteration 3
    }
    console.log(i);
}
// Outputs: 1, 2, 4, 5 (Notice 3 is missing)
```

---

## Exercises and Review

Put your control flow knowledge into practice with these challenges:

1. **FizzBuzz Lite:** Write a `for` loop that counts from $1$ to $15$. 
   * If the number is divisible by $3$, log `"Fizz"` to the console instead of the number.
   * If the number is divisible by $5$, log `"Buzz"`.
   * Otherwise, log the number itself.
2. **Switch Conversion:** Convert this `if-else` statement into an equivalent `switch` statement:
   ```javascript
   let grade = "B";
   if (grade === "A") {
       console.log("Excellent job!");
   } else if (grade === "B") {
       console.log("Good work!");
   } else {
       console.log("Keep trying!");
   }
   ```
3. **The Secure Login:** Write a loop that mimics entering a passcode. Create a variable `correctPin = "1234"`. Write a `while` loop that prompts for input or checks attempts. Limit the loop to run a maximum of $3$ times. If the correct code is guessed, print `"Access Granted"` and break out of the loop early. If all $3$ attempts are used, print `"Account Locked"`.
