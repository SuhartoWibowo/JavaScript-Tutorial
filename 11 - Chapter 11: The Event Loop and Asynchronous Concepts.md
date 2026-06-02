# Chapter 11: The Event Loop and Asynchronous Concepts

We are now transitioning into one of the most critical aspects of JavaScript development: **Asynchronous Programming**. 

JavaScript is defined as a **single-threaded** programming language. This means it can only execute one piece of code at a time, using a single sequence of instructions. If JavaScript is single-threaded, how can it fetch data from a server, listen to user clicks, and run timers all at once without freezing the entire browser interface?

The answer lies in how the JavaScript runtime coordinates with its host environment (such as the web browser or Node.js). In this chapter, we will look at how the **Call Stack**, **Web APIs**, the **Microtask Queue**, and the **Event Loop** work together to achieve asynchronous behavior.

---

## 11.1 Single-Threaded JavaScript and the Call Stack

To understand how asynchronous code works, we first need to understand how synchronous code is managed. JavaScript uses a data structure called the **Call Stack** to keep track of function execution.

The Call Stack works on a **LIFO (Last In, First Out)** basis. When a function is called, it is pushed onto the top of the stack. When the function returns a value, it is popped off the top of the stack.

```javascript
function stepThree() {
    console.log("Step Three");
}

function stepTwo() {
    stepThree();
}

function stepOne() {
    stepTwo();
}

stepOne();
```

If you were to pause execution inside `stepThree`, the Call Stack would look like this:

```
+---------------------+
|   stepThree()       |  <-- Current executing function (Top)
+---------------------+
|   stepTwo()         |
+---------------------+
|   stepOne()         |
+---------------------+
|   global context    |  <-- Bottom of the stack
+---------------------+
```

Because there is only one Call Stack, if a function takes a very long time to execute (such as a massive calculation or waiting for a slow network request), the stack is **blocked**. While the stack is blocked, the browser cannot run any other code, respond to clicks, or repaint the screen, causing the page to feel frozen.

---

## 11.2 The Asynchronous Ecosystem (Web APIs)

To prevent blocking, we use asynchronous code. While the core JavaScript engine is indeed single-threaded, the environment it runs in (the web browser, for instance) is not. Browsers provide extra multi-threaded features called **Web APIs**.

Web APIs include:
*   Timers (like `setTimeout` and `setInterval`)
*   Network requests (like the `fetch` API)
*   DOM event listeners (like clicking buttons)

When you invoke a Web API, JavaScript hands the task over to the browser's background threads and immediately moves on to the next line of code. The Call Stack is never blocked.

```javascript
console.log("First");

// Handed off to the browser's timer thread
setTimeout(() => {
    console.log("Second");
}, 1000);

console.log("Third");
```

If you run this code, the console displays:
1.  `First`
2.  `Third`
3.  `Second` (after approximately 1 second)

---

## 11.3 Queues: Macrotasks vs. Microtasks

Once a Web API task is complete (for example, the 1000ms timer runs out), its callback function cannot simply force its way back onto the Call Stack—doing so might interrupt a function that is already running.

Instead, completed asynchronous callback functions are placed into waiting lines called **Queues**. There are two separate queues that JavaScript manages.

### 1. The Callback Queue (Macrotask Queue)
This queue holds standard asynchronous callbacks.
*   **Sources:** `setTimeout`, `setInterval`, user interaction events (like clicks), and I/O tasks.

### 2. The Microtask Queue
This queue has a higher priority than the Callback Queue. The JavaScript runtime will execute all tasks in this queue before checking the Callback Queue.
*   **Sources:** Promise callbacks (such as `.then()`, `.catch()`, and `.finally()`), `MutationObserver`, and functions queued explicitly via `queueMicrotask()`.

---

## 11.4 How the Event Loop Coordinates Execution

The **Event Loop** is a constantly running background process that coordinates the Call Stack, the Microtask Queue, and the Callback Queue.

```
+-------------------------------------------------+
|                   CALL STACK                    |
|                                                 |
+-------------------------------------------------+
                         ^
                         | (Pushes task when stack is empty)
                         |
+-------------------------------------------------+
|                  EVENT LOOP                     |
+-------------------------------------------------+
         ^                               ^
         | (Checks Microtasks first)     | (Checks Macrotasks second)
         |                               |
+------------------+           +------------------+
|  MICROTASK QUEUE |           |  CALLBACK QUEUE  |
|  (Promises)      |           |  (setTimeout)    |
+------------------+           +------------------+
```

### The Event Loop Algorithm
1.  **Run Synchronous Code:** Execute all scripts currently on the Call Stack until the stack is completely empty.
2.  **Clear Microtasks:** Check the **Microtask Queue**. If there are tasks, execute them one by one until the queue is completely empty. (If a microtask adds *another* microtask, it also runs in this cycle).
3.  **Check Macrotasks:** If the Call Stack is empty and the Microtask Queue is empty, take the **first** task from the **Callback Queue** (Macrotask Queue), push it to the Call Stack, and execute it.
4.  **Repeat:** Return to Step 1.

### Tracing the Order of Execution
Let's analyze a classic interview question designed to test your understanding of the Event Loop:

```javascript
console.log("1. Global script start");

// Macrotask
setTimeout(() => {
    console.log("2. Timeout callback");
}, 0);

// Microtask
Promise.resolve().then(() => {
    console.log("3. Promise resolved callback");
});

console.log("4. Global script end");
```

If you run this code, the output order is:
```
1. Global script start
4. Global script end
3. Promise resolved callback
2. Timeout callback
```

### Why does this happen?
1.  `1. Global script start` is printed immediately because it is synchronous.
2.  `setTimeout(..., 0)` registers a timer with the browser. Even though the time delay is $0$ milliseconds, the browser immediately places its callback in the **Callback Queue (Macrotask Queue)**.
3.  `Promise.resolve().then(...)` registers a callback. Because it is a Promise, the callback is placed in the **Microtask Queue**.
4.  `4. Global script end` is printed because it is synchronous. The synchronous global script is now complete, and the Call Stack is empty.
5.  The **Event Loop** looks at the queues. It sees tasks in both queues. Because the **Microtask Queue** has priority, it executes the Promise callback, printing `3. Promise resolved callback`.
6.  The Microtask Queue is now empty. The Event Loop looks at the **Callback Queue** and executes the timer callback, printing `2. Timeout callback`.

---

## Exercises and Review

Develop your mental model of the Event Loop by working through these scenarios:

1. **The Order Predictor:** Predict the precise output order of this script without running it. Once you have made your prediction, run it in your browser console to verify your answer:
   ```javascript
   console.log("A");
   
   setTimeout(() => {
       console.log("B");
   }, 0);
   
   Promise.resolve().then(() => {
       console.log("C");
   });
   
   queueMicrotask(() => {
       console.log("D");
   });
   
   console.log("E");
   ```
2. **Synchronous Blocking Simulation:** Write a function called `blockStack(seconds)` that uses a basic `while` loop checking `Date.now()` to pause execution for a set number of seconds. Write a script that logs `"Start"`, sets a `setTimeout` for $0$ms, runs `blockStack(3)`, and logs `"End"`. Observe how the timeout callback is delayed significantly past $0$ms due to the blocked stack.
3. **Queue Comparison Reflection:** In your own words, explain the structural difference between the Microtask Queue and the Callback (Macrotask) Queue. Why is it beneficial for promises to use the Microtask Queue?
