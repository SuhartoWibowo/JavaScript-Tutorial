# Chapter 7: Objects and Key-Value Collections

In the previous chapter, we used arrays to manage ordered lists of data. However, real-world data is often more complex. For example, if you wanted to represent a user in a system, storing their name, age, and email in an array (`["Alice", 25, "alice@email.com"]`) makes it difficult to remember which value represents what.

To handle structured data, JavaScript provides **objects**. An object is a collection of related data and behavior, stored as a set of **key-value pairs**. In this chapter, we will learn how to create and manipulate objects, extract data cleanly using destructuring, and work with modern key-value collections like `Map` and `Set`.

---

## 7.1 Creating Objects, Properties, and Methods

We define an object using curly braces `{}` (called an object literal). Inside, we write keys (also called properties) and pair them with values.

```javascript
const user = {
    firstName: "Alice",
    age: 25,
    isSubscribed: true
};
```
In this example, `firstName`, `age`, and `isSubscribed` are **keys** (or property names), and `"Alice"`, `25`, and `true` are their corresponding **values**.

### Adding Behavior: Methods
If a value stored inside an object is a function, it is called a **method**. Methods represent the actions or behaviors an object can perform.

```javascript
const dog = {
    name: "Rex",
    breed: "German Shepherd",
    bark: function() {
        console.log("Woof! Woof!");
    },
    // Shorthand syntax introduced in ES6
    sleep() {
        console.log("Zzz...");
    }
};

// Calling a method
dog.bark();  // Outputs: "Woof! Woof!"
dog.sleep(); // Outputs: "Zzz..."
```

---

## 7.2 Accessing Properties

You can retrieve, add, modify, or delete properties from an object using either **Dot Notation** or **Bracket Notation**.

### 1. Dot Notation
Dot notation is the most common and readable way to access properties.

```javascript
const player = { name: "Mario", score: 64 };

// Reading a property
console.log(player.name); // Outputs: "Mario"

// Modifying a property
player.score = 128;

// Adding a new property
player.lives = 3;

console.log(player); // { name: "Mario", score: 128, lives: 3 }
```

### 2. Bracket Notation
Bracket notation requires you to pass the key as a string inside square brackets `[]`. It is necessary in two situations:
1.  The key name contains special characters or spaces.
2.  The key name is dynamic (stored inside a variable).

```javascript
const book = {
    title: "The Hobbit",
    "publication-year": 1937
};

// 1. Handling keys with special characters
console.log(book["publication-year"]); // Outputs: 1937

// 2. Handling dynamic keys using a variable
const propertyToAccess = "title";
console.log(book[propertyToAccess]); // Outputs: "The Hobbit"
```

---

## 7.3 Object Destructuring and the Spread Operator

Modern JavaScript provides tools to work with objects with less boilerplate code.

### Object Destructuring
Destructuring allows you to unpack properties from an object and assign them to individual variables using a highly concise syntax.

```javascript
const laptop = {
    brand: "Apple",
    model: "MacBook Pro",
    ram: 16
};

// Traditional way
// const brand = laptop.brand;
// const model = laptop.model;

// Modern Destructuring
const { brand, model, ram } = laptop;

console.log(brand); // "Apple"
console.log(ram);   // 16
```

### The Object Spread Operator (`...`)
Just like with arrays, the spread operator allows you to copy or merge objects cleanly.

```javascript
const baseSettings = { theme: "dark", volume: 80 };

// Copy and override/add properties
const userSettings = {
    ...baseSettings,
    volume: 100, // Overrides the original value
    notifications: true // Adds a new property
};

console.log(userSettings); // { theme: "dark", volume: 100, notifications: true }
```

---

## 7.4 Modern Collections: Map and Set

While plain objects are highly versatile, they have limitations (such as keys only being allowed to be strings or symbols). ES6 introduced `Map` and `Set` to address specific collection needs.

### 1. Map
A `Map` is a collection of keyed data items, similar to an object. However, a `Map` allows keys of **any type** (including numbers, booleans, and even other objects) and preserves the exact insertion order of the items.

```javascript
const roleMap = new Map();

// Setting key-value pairs
roleMap.set("admin", "Full Access");
roleMap.set(12, "User ID Twelve"); // Number as key

// Getting values
console.log(roleMap.get("admin")); // Outputs: "Full Access"
console.log(roleMap.has("editor")); // Outputs: false

// Checking the size
console.log(roleMap.size); // Outputs: 2
```

### 2. Set
A `Set` is a collection of **unique values**. Unlike arrays, a `Set` cannot contain duplicate values. This makes it an excellent tool for tracking unique items or filtering duplicates out of an array.

```javascript
const uniqueNumbers = new Set();

uniqueNumbers.add(5);
uniqueNumbers.add(10);
uniqueNumbers.add(5); // Ignored because it is a duplicate

console.log(uniqueNumbers); // Set(2) { 5, 10 }
console.log(uniqueNumbers.has(10)); // true

// Removing duplicates from an array
const duplicateArray = [1, 2, 2, 3, 4, 4, 4, 5];
const pristineArray = [...new Set(duplicateArray)];
console.log(pristineArray); // [1, 2, 3, 4, 5]
```

---

## Exercises and Review

Put your object skills to work with these exercises:

1. **The Profiler:** Create an object named `myProfile`. Give it properties like `name` (string), `skills` (array of strings), and an object property named `contact` (which contains `email` and `phone`). Practice using destructuring to extract your `name` and `email` properties.
2. **Dynamic Menu:** You are building a food delivery app. Write a function named `addDiscount` that takes a `menuItem` object and a `discountAmount` (number) as arguments. The function should use the spread operator to return a new menu item object with its price updated by subtracting the discount.
3. **The Duplicate Cleaner:** Write a function named `cleanList` that accepts an array of strings representing usernames, removes any duplicates, and returns a clean array sorted alphabetically. (Hint: combine `Set` and standard array sorting methods).
