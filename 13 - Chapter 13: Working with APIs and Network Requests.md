# Chapter 13: Working with APIs and Network Requests

Modern web applications rarely function in complete isolation. To display real-time weather, retrieve user posts, or submit a payment, your application must communicate with external servers over the internet. This interaction is typically handled by interacting with **Application Programming Interfaces (APIs)**.

In this chapter, we will learn how data is structured for transmission across networks, how to make HTTP requests using the browser's built-in `fetch` API, how to handle network errors correctly, and how to execute multiple requests concurrently to optimize application performance.

---

## 13.1 Introduction to JSON (JavaScript Object Notation)

When sending data between a client (your browser) and a server, both systems need a mutually understood format. Today, the standard format for web data transfer is **JSON (JavaScript Object Notation)**.

Although JSON looks almost identical to a standard JavaScript object literal, it is actually a plain text string representation of data. It has strict formatting rules:
1.  All property names (keys) must be wrapped in double quotes (`"key"`). Single quotes are invalid.
2.  String values must also be wrapped in double quotes.
3.  No functions, methods, comments, or trailing commas are allowed.

```json
{
  "id": 101,
  "username": "dev_alice",
  "isPremium": true,
  "roles": ["editor", "admin"]
}
```

### Parsing and Stringifying
JavaScript provides a built-in `JSON` object with two methods to convert data between text and active memory objects:

*   **`JSON.parse(string)`:** Converts a JSON text string into a usable JavaScript object.
*   **`JSON.stringify(object)`:** Converts a JavaScript object into a JSON text string, ready to be sent over a network.

```javascript
const jsonString = '{"name": "Bob", "age": 30}';

// 1. Parsing text into an object
const userObj = JSON.parse(jsonString);
console.log(userObj.name); // Outputs: "Bob"

// 2. Formatting an object into text
const rawText = JSON.stringify(userObj);
console.log(typeof rawText); // Outputs: "string"
```

---

## 13.2 Making HTTP Requests using the `fetch` API

The **Hypertext Transfer Protocol (HTTP)** defines how clients and servers request and deliver resources. The most common HTTP verbs are:
*   `GET`: Retrieve data from a server.
*   `POST`: Send new data to a server.
*   `PUT`/`PATCH`: Update existing data on a server.
*   `DELETE`: Remove data from a server.

To make an HTTP request in modern JavaScript, we use the global **`fetch()`** function, which returns a Promise.

### A Basic GET Request
Let's fetch mock data from a public API using `async/await`:

```javascript
async function getTodoData() {
    try {
        // Step 1: Call fetch. It resolves to a Response object once headers are received.
        const response = await fetch("https://jsonplaceholder.typicode.com/todos/1");
        
        // Step 2: Read and parse the response body as JSON. This is also asynchronous!
        const todo = await response.json(); 
        
        console.log(todo);
    } catch (error) {
        console.error("Network request failed:", error);
    }
}

getTodoData();
```

---

## 13.3 Headers, Body, and POST Requests

When retrieving data (`GET`), you do not need to send a message body. However, when creating resources (`POST`), you must provide extra configuration options to `fetch()`, such as specifying the HTTP method, the headers (metadata about the request), and the actual payload (body).

```javascript
async function createNewPost() {
    const postData = {
        title: "Learning JavaScript",
        body: "I am currently mastering asynchronous programming.",
        userId: 1
    };

    try {
        const response = await fetch("https://jsonplaceholder.typicode.com/posts", {
            method: "POST", // Specify HTTP method
            headers: {
                // Inform the server that the payload is formatted as JSON
                "Content-Type": "application/json" 
            },
            body: JSON.stringify(postData) // Convert object to JSON text string
        });

        const result = await response.json();
        console.log("Success! Resource created:", result);
    } catch (error) {
        console.error("Failed to create post:", error);
    }
}

createNewPost();
```

### Critical Gotcha: Handling HTTP Error Statuses
A common point of confusion is how `fetch` handles errors. The `fetch` Promise **will not reject** on HTTP error status codes like `404` (Not Found) or `500` (Internal Server Error). It only rejects if the request physically fails to complete (e.g., loss of internet connection or DNS resolution issues).

To verify if a request succeeded, you must inspect the **`response.ok`** property (which is `true` if the status code is in the 200–299 range) or the **`response.status`** code directly.

```javascript
async function fetchSafe() {
    const response = await fetch("https://jsonplaceholder.typicode.com/invalid-route");
    
    // Explicitly check for success
    if (!response.ok) {
        throw new Error(`HTTP Error! Status: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
}
```

---

## 13.4 Concurrent Requests: `Promise.all` and Friends

If you need to make three independent network requests, awaiting them sequentially will slow down your application because the second request cannot start until the first finishes:

```javascript
// Slow sequential execution (takes ~3 seconds total)
const user = await fetchUser(); // Takes 1s
const posts = await fetchPosts(); // Takes 1s
const albums = await fetchAlbums(); // Takes 1s
```

To run requests **concurrently (in parallel)**, JavaScript provides Promise combinators.

### 1. `Promise.all()`
`Promise.all` accepts an array of Promises and returns a single Promise. It resolves when **all** promises in the array have successfully resolved. However, if **any** promise rejects, the entire operation rejects immediately (*all-or-nothing* behavior).

```javascript
async function loadDashboardData() {
    try {
        // Starts all three requests at the same time
        const [userData, postData] = await Promise.all([
            fetch("https://jsonplaceholder.typicode.com/users/1").then(r => r.json()),
            fetch("https://jsonplaceholder.typicode.com/posts?userId=1").then(r => r.json())
        ]);
        
        console.log("Dashboard loaded.", { userData, postData });
    } catch (error) {
        console.error("One of the requests failed:", error);
    }
}
```

### 2. `Promise.allSettled()`
If you want to run multiple requests and don't want the failure of one request to block the others, use `Promise.allSettled`. It waits for all promises to resolve or reject and returns an array of objects describing the outcome of each.

```javascript
async function loadPartiallyFailingData() {
    const promises = [
        fetch("https://jsonplaceholder.typicode.com/users/1").then(r => r.json()),
        fetch("https://jsonplaceholder.typicode.com/invalid-route").then(r => r.json()) // Will fail
    ];

    const results = await Promise.allSettled(promises);
    
    results.forEach((result, index) => {
        if (result.status === "fulfilled") {
            console.log(`Promise ${index} succeeded:`, result.value);
        } else {
            console.log(`Promise ${index} failed:`, result.reason);
        }
    });
}
```

### 3. `Promise.race()`
`Promise.race` returns a promise that resolves or rejects as soon as the **first** promise in the input array resolves or rejects. It is commonly used to implement request timeouts.

---

## Exercises and Review

Develop your data fetching skills with these challenges:

1. **The Safe Fetcher:** Write an `async` function called `fetchUserAndValidate(id)` that queries `https://jsonplaceholder.typicode.com/users/id`. Use a `try...catch` block. Ensure you inspect `response.ok` and throw a descriptive error if the user is not found.
2. **The JSON Converter:** Create a JavaScript object with variables, nested objects, and an array. Practice stringifying it, printing the resulting string, and parsing it back into a separate object variable. 
3. **The Race Tracker:** Create two promises: one that resolves after $1$ second with `"Winner A"`, and one that resolves after $2$ seconds with `"Winner B"`. Use `Promise.race` to determine which completes first and output the result.
