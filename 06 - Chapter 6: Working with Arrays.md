# Chapter 6: Working with Arrays

In previous chapters, we worked with single values stored in variables, such as a number, a string, or a boolean. However, as applications grow, you will frequently need to work with collections of data—like a list of items in a shopping cart, a list of users, or a collection of temperature readings.

To handle these collections, JavaScript provides a built-in data structure called an **array**. An array is an ordered list of values where each value is called an **element**. In this chapter, we will learn how to create arrays, manipulate their contents, search through them, and transform them using modern array methods.

---

## 6.1 Creating Arrays and Basic Manipulation

You create an array by wrapping a comma-separated list of values in square brackets `[]`. Arrays can hold any type of data, and they can even hold a mix of different data types (though keeping them uniform is standard practice).

```javascript
const fruits = ["apple", "banana", "cherry"];
```

### Accessing Elements and Zero-Based Indexing
Every element in an array has a numbered position, known as its **index**. JavaScript arrays are **zero-indexed**, meaning the first element is at index `0`, the second is at index `1`, and so on.

```javascript
console.log(fruits[0]); // Outputs: "apple"
console.log(fruits[2]); // Outputs: "cherry"

// Checking the total number of items in the array
console.log(fruits.length); // Outputs: 3
```

### Adding and Removing Elements
JavaScript provides several built-in methods to modify an array by adding or removing elements at either the beginning or the end.

```javascript
const colors = ["green", "blue"];

// Add to the end of the array
colors.push("red"); 
console.log(colors); // ["green", "blue", "red"]

// Remove from the end of the array (returns the removed element)
const lastColor = colors.pop(); 
console.log(lastColor); // "red"
console.log(colors);    // ["green", "blue"]

// Add to the beginning of the array
colors.unshift("yellow");
console.log(colors); // ["yellow", "green", "blue"]

// Remove from the beginning of the array (returns the removed element)
const firstColor = colors.shift();
console.log(firstColor); // "yellow"
console.log(colors);     // ["green", "blue"]
```

---

## 6.2 Searching Arrays

When working with arrays, you often need to check if an element exists or locate its position.

### Searching for Primitives: `indexOf` and `includes`
For simple values (like strings or numbers), you can search directly:

```javascript
const pets = ["cat", "dog", "bird"];

// includes() returns true or false
console.log(pets.includes("dog"));  // true
console.log(pets.includes("horse")); // false

// indexOf() returns the index of the first match, or -1 if not found
console.log(pets.indexOf("bird"));  // 2
console.log(pets.indexOf("horse")); // -1
```

### Searching with Conditions: `find` and `findIndex`
If you need to search an array based on a custom condition (rather than a direct value match), you use `find` or `findIndex`. These methods accept a callback function that evaluates each element.

```javascript
const numbers = [5, 12, 8, 130, 44];

// find() returns the value of the FIRST element that passes the test
const found = numbers.find(num => num > 10);
console.log(found); // 12

// findIndex() returns the index of the FIRST element that passes the test
const foundIndex = numbers.findIndex(num => num > 10);
console.log(foundIndex); // 1 (which is the index of 12)
```

---

## 6.3 Essential Iterative Methods

Modern JavaScript relies heavily on functional methods to iterate over, transform, and evaluate arrays without manually writing traditional `for` loops. These methods accept a callback function as an argument.

### 1. `forEach`
The `forEach` method executes a provided function once for each array element. It is used to perform side effects (like logging to the console) and does not return anything.

```javascript
const names = ["Alice", "Bob", "Charlie"];
names.forEach(name => console.log(`Hello, ${name}!`));
```

### 2. `map`
The `map` method creates a **new array** populated with the results of calling a provided function on every element in the calling array. It does not modify the original array.

```javascript
const prices = [10, 20, 30];
const discountedPrices = prices.map(price => price * 0.9);

console.log(discountedPrices); // Outputs: [9, 18, 27]
console.log(prices);           // Original array remains: [10, 20, 30]
```

### 3. `filter`
The `filter` method creates a **new array** containing only the elements that pass a test implemented by the provided function.

```javascript
const grades = [55, 82, 91, 68, 74];
const passingGrades = grades.filter(grade => grade >= 70);

console.log(passingGrades); // Outputs: [82, 91, 74]
```

### 4. `reduce`
The `reduce` method executes a user-provided callback function on each element of the array, passing in the return value from the calculation on the preceding element. This reduces the entire array down to a **single value** (like a sum or a concatenated string).

```javascript
const bills = [5, 15, 25];

// The callback takes two primary parameters: the accumulator (running total) and the current value
// The 0 at the end is the initial value of the accumulator
const totalBill = bills.reduce((accumulator, currentValue) => {
    return accumulator + currentValue;
}, 0);

console.log(totalBill); // Outputs: 45
```

---

## 6.4 The Spread Operator (`...`) with Arrays

Introduced in ES6, the spread operator (`...`) allows you to quickly copy or combine arrays.

```javascript
const primaryColors = ["red", "yellow", "blue"];
const secondaryColors = ["orange", "green", "violet"];

// 1. Copying an array (creates a shallow copy instead of copying by reference)
const colorsCopy = [...primaryColors];
console.log(colorsCopy); // ["red", "yellow", "blue"]

// 2. Combining arrays
const allColors = [...primaryColors, ...secondaryColors];
console.log(allColors); // ["red", "yellow", "blue", "orange", "green", "violet"]
```

---

## Exercises and Review

Put your array skills to the test with these challenges:

1. **The Array Shuffler:** Create an array of $5$ items. Write a sequence of code that removes the first item from the array and adds it to the end.
2. **Transforming Data:** You are given an array of Celsius temperatures: `[0, 15, 25, 30, 40]`. Use the `map` method to convert all of them into Fahrenheit. (Formula: $F = C \times 1.8 + 32$).
3. **The Accountant:** You are given an array of transaction amounts (some positive deposits, some negative withdrawals): `[100, -25, 50, -10, -5, 200]`.
   * Use `filter` to create an array containing only the withdrawals (negative numbers).
   * Use `reduce` to calculate the net balance of all transactions combined.
