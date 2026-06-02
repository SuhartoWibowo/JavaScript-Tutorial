# Chapter 12: Callbacks, Promises, and Async/Await

In the previous chapter, we examined the internal architecture of the Event Loop to understand *how* JavaScript manages asynchronous execution behind the scenes. In this chapter, we will focus on *how to write* asynchronous code.

Asynchronous patterns in JavaScript have evolved significantly. To manage actions like loading a file or calling an API, developers transitioned from using nested functions, to using structured objects, and finally to modern, linear syntax that reads similarly to synchronous code. We will trace this evolution and learn how to implement these patterns in your own applications.

---

## 12.1 The Evolution: From Callbacks to Callback Hell

Historically, the only way to execute code after an asynchronous operation completed was to pass a function as an argument to another function. This passed function is known as a **callback**.

```javascript
// A function simulating fetching user data from a database
function fetchUser(userId, callback) {
    setTimeout(() => {
        console.log("User data retrieved.");
        callback({ id: userId, username: "dev_alice" });
    }, 1000);
}

fetchUser(101, (user) => {
    console.log(`Welcome, ${user.username}`);
});
```

### Callback Hell (The Pyramid of Doom)
Using one or two callbacks is manageable. However, if you need to perform multiple dependent asynchronous steps in a sequence—such as retrieving a user, fetching their post list, and then loading comments for a specific post—your code must be deeply nested.

```javascript
// Conceptual representation of Callback Hell
getUser(101, (user) => {
    getPosts(user.id, (posts) => {
        getComments(posts[0].id, (comments) => {
            getAuthorDetails(comments[0].authorId, (author) => {
                console.log(author);
                // The nesting continues...
            });
        });
    });
});
```

This structural pattern is referred to as **Callback Hell** or the **Pyramid of Doom**. It is difficult to read, hard to debug, and makes error handling across the steps complicated.

---

## 12.2 Understanding Promises

To solve the nesting issue, ES6 introduced the **Promise**. A Promise is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value.

At any given moment, a Promise exists in one of three states:

1.  **Pending:** The initial state. The asynchronous operation has started but has not finished yet.
2.  **Fulfilled:** The operation completed successfully. The promise now holds the resolved value.
3.  **Rejected:** The operation failed. The promise now holds an error or reason for the failure.

### Creating a Promise
You construct a Promise using the `new Promise` constructor, which accepts a callback function (known as the *executor*) with two parameters: `resolve` and `reject`.

```javascript
const fetchFile = new Promise((resolve, reject) => {
    let success = true; // Simulating outcome of an operation
    
    setTimeout(() => {
        if (success) {
            resolve("File content loaded successfully."); // Changes state to Fulfilled
        } else {
            reject("Error: File could not be found.");    // Changes state to Rejected
        }
    }, 1000);
});
```

---

## 12.3 Consuming Promises: `.then()`, `.catch()`, and `.finally()`

To retrieve the values or errors from a Promise, we use three built-in handler methods.

```javascript
fetchFile
    .then((data) => {
        // Runs only if the promise is fulfilled
        console.log(data); 
    })
    .catch((error) => {
        // Runs only if the promise is rejected
        console.error(error); 
    })
    .finally(() => {
        // Runs regardless of the promise outcome (fulfilled or rejected)
        console.log("Operation complete."); 
    });
```

### Promise Chaining
Promises solve Callback Hell because every time a handler method (`.then()`) returns a value or another Promise, it returns a *new* Promise. This allows us to chain asynchronous operations vertically rather than nesting them horizontally.

```javascript
// Resolving Callback Hell with Promise Chaining
getUser(101)
    .then(user => getPosts(user.id))
    .then(posts => getComments(posts[0].id))
    .then(comments => getAuthorDetails(comments[0].authorId))
    .then(author => console.log(author))
    .catch(error => console.error("An error occurred at some step:", error));
```
If an error occurs at *any* point in this chain, execution stops immediately, and control jumps straight to the single `.catch()` block at the bottom.

---

## 12.4 Modern Asynchronous Syntax: `async` and `await`

While Promise chaining is a major improvement over raw callbacks, it can still become verbose. In ES2017 (ES8), JavaScript introduced **`async` and `await`**, which acts as syntactic sugar built on top of Promises. It allows you to write asynchronous code that reads like synchronous, line-by-line code.

### 1. The `async` Keyword
You place the `async` keyword before any function definition. An `async` function always returns a Promise. If you return a direct value from an `async` function, JavaScript automatically wraps it in a resolved Promise.

```javascript
async function getGreeting() {
    return "Hello!"; // Automatically converted to a Promise
}

getGreeting().then(msg => console.log(msg)); // Outputs: "Hello!"
```

### 2. The `await` Keyword
The `await` keyword can only be used inside an `async` function (or at the top level of modern ES modules). It pauses the execution of the function, waiting for the Promise to resolve or reject, before proceeding to the next line.

```javascript
// Simulating an asynchronous database query
function fetchDatabaseRecords() {
    return new Promise((resolve) => {
        setTimeout(() => resolve(["User A", "User B"]), 1500);
    });
}

// Consuming the promise with async/await
async function showRecords() {
    console.log("Fetching records...");
    
    // The execution of this function pauses here until fetchDatabaseRecords resolves
    const records = await fetchDatabaseRecords(); 
    
    console.log("Records received:", records);
}

showRecords();
```

---

## 12.5 Error Handling with `try...catch`

When using Promises with `.then()`, you handle errors using `.catch()`. When using `async/await`, you handle errors using standard synchronous JavaScript **`try...catch`** blocks.

```javascript
function loadSecureData() {
    return new Promise((resolve, reject) => {
        setTimeout(() => reject("Unauthorized Access!"), 1000);
    });
}

async function displaySecureData() {
    try {
        const data = await loadSecureData();
        console.log(data);
    } catch (error) {
        // Handles any promise rejection or standard code errors within the block
        console.warn(`Failed to display data. Reason: ${error}`);
    }
}

displaySecureData();
// Outputs: "Failed to display data. Reason: Unauthorized Access!" after 1 second
```

Using `try...catch` keeps error boundaries clean and allows developers to manage both synchronous syntax errors and asynchronous promise rejections inside the same block of code.

---

## Exercises and Review

Practice transitioning asynchronous code structures with these challenges:

1. **Callback to Promise Conversion:** Here is an older, callback-based function. Rewrite it so that it returns a Promise instead of using a callback parameter:
   ```javascript
   function delayedMessage(text, callback) {
       setTimeout(() => {
           callback(text);
       }, 2000);
   }
   ```
2. **The Sequential Runner:** Create two mock operations: `fetchID()` (returns a number after $1$ second) and `fetchUsername(id)` (returns a name string after $1$ second). Write an `async` function that waits for `fetchID()`, passes the resulting ID to `fetchUsername()`, and finally prints the username.
3. **The Chaos Engine:** Write a function `rollChance()` that returns a Promise. The promise should resolve with `"Jackpot"` 50% of the time, and reject with `"Bankrupt"` 50% of the time. Write an `async` function that calls `rollChance()` using a `try...catch` block to handle both outcomes gracefully.
