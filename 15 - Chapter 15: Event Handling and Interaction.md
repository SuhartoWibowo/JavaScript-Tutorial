# Chapter 15: Event Handling and Interaction

A static web page becomes an interactive application when it can react to user input. When a user clicks a button, types in a text field, submits a form, or scrolls down a page, the browser generates an **Event**.

In this chapter, we will explore how to listen for and respond to these events using **Event Listeners**. We will also examine the **Event Object** to extract metadata about user actions, investigate how events travel through the DOM (known as **Event Propagation**), and implement a highly efficient design pattern called **Event Delegation**.

---

## 15.1 Adding Event Listeners

To react to an event, we must register a function that "listens" for that event to occur on a specific DOM element. The modern and standard way to achieve this is by using the `addEventListener()` method.

**Syntax:** `element.addEventListener(eventType, callbackFunction)`

```javascript
const actionButton = document.querySelector("#action-btn");

// Defining the callback function
function handleButtonClick() {
    console.log("Button was clicked!");
}

// Registering the listener
actionButton.addEventListener("click", handleButtonClick);
```

### Why Use `addEventListener`?
Earlier versions of JavaScript relied on inline HTML attributes (like `<button onclick="alert('hi')">`) or element properties (like `btn.onclick = handleButtonClick`). Modern developers avoid those methods because `addEventListener`:
1.  Allows you to attach multiple, independent listeners to the exact same event on a single element.
2.  Provides a clean separation of concerns, keeping HTML markup strictly for structure and JavaScript for behavior.
3.  Allows you to cleanly remove listeners later using `removeEventListener()`.

---

## 15.2 The Event Object

When an event occurs, the browser automatically passes an argument to your callback function. This argument is the **Event Object** (conventionally named `event` or simply `e`). It contains metadata about the event that just took place.

```javascript
const emailInput = document.querySelector("#email-field");

emailInput.addEventListener("keydown", (e) => {
    console.log(`User pressed the key: ${e.key}`);
});
```

### Essential Properties and Methods of the Event Object

#### 1. `e.target`
Points directly to the DOM element that triggered the event. This is useful for identifying which element was interacted with.

```javascript
document.addEventListener("click", (e) => {
    console.log(`You clicked on: ${e.target.tagName}`);
});
```

#### 2. `e.preventDefault()`
Some events have default browser actions. For example, clicking a link automatically navigates to a new URL, and submitting a form reloads the entire page. You can intercept and stop these default behaviors using `e.preventDefault()`.

```javascript
const registrationForm = document.querySelector("#register-form");

registrationForm.addEventListener("submit", (e) => {
    // Stop the browser from submitting the form and reloading the page
    e.preventDefault(); 
    
    console.log("Form submission intercepted. Processing via JavaScript...");
});
```

---

## 15.3 Event Propagation: Bubbling and Capturing

If you have a button inside a paragraph, and that paragraph is inside a section, clicking the button means you are technically clicking all three elements. How does JavaScript determine which click listener to fire first?

```html
<section id="outer">
    <p id="middle">
        <button id="inner">Click Me!</button>
    </p>
</section>
```

This behavior is governed by **Event Propagation**, which happens in two primary phases:

1.  **Capturing Phase:** The event starts from the top of the DOM tree (the `window` object) and travels down to the target element.
2.  **Bubbling Phase (Default):** Once the target is reached, the event "bubbles" back up, traveling from the target element up to the parent elements until it reaches the root.

```
       1. CAPTURING PHASE (Down)
       [ window ] ---> [ outer ] ---> [ middle ]
                                          |
                                          v
                                      [ inner ] (Target)
                                          |
       2. BUBBLING PHASE (Up)             |
       [ window ] <--- [ outer ] <--- [ middle ]
```

### Stopping Propagation (`e.stopPropagation()`)
If you have click event listeners on both a parent and a child element, clicking the child will trigger both listeners due to bubbling. You can stop the event from continuing its journey up the DOM tree by using `e.stopPropagation()`.

```javascript
const parent = document.querySelector("#outer");
const child = document.querySelector("#inner");

parent.addEventListener("click", () => {
    console.log("Parent clicked!");
});

child.addEventListener("click", (e) => {
    // Prevents the click event from bubbling up to the parent element
    e.stopPropagation(); 
    console.log("Child clicked!");
});
```

---

## 15.4 Event Delegation

Suppose you are building a to-do list application, and the user can add dozens of items. If you add a click listener to every single list item individually to handle deletion, your application will consume a lot of memory and slow down. Furthermore, when you add new items dynamically, you must remember to bind listeners to them as well.

To solve this, we use a pattern called **Event Delegation**. 

Instead of adding listeners to individual children, we add **one single event listener** to a common parent element. When a child element is clicked, the click event bubbles up to the parent, where we inspect `e.target` to determine exactly which child was clicked.

```javascript
const listContainer = document.querySelector("#todo-list");

// Add one single listener to the parent element
listContainer.addEventListener("click", (e) => {
    // Check if the clicked element was specifically a delete button
    if (e.target.classList.contains("delete-btn")) {
        // e.target.parentElement gets the list item containing the button
        const listItem = e.target.parentElement;
        listItem.remove();
        console.log("Item deleted using event delegation!");
    }
});
```

### Why use Event Delegation?
1.  **Memory Efficiency:** One event listener uses significantly fewer system resources than hundreds of individual listeners.
2.  **Handles Dynamic Elements:** If you dynamically append new items to the `#todo-list` container in the future, they automatically work with the parent's event listener without needing any new handlers bound to them.

---

## Exercises and Review

Put your interactive JavaScript skills to the test:

1. **The Character Counter:** Create a text area element `<textarea id="message-box"></textarea>` and a helper span `<span id="counter">0</span>`. Write an event listener in JavaScript that tracks input on the textarea and updates the counter span with the length of the string currently typed.
2. **The Bubble Interrupter:** Set up a parent div containing a child button. Add a click handler to both. Write the code so that:
   * Clicking the button logs `"Button clicked!"` and stops propagation.
   * Clicking the background of the div logs `"Background clicked!"`.
3. **Delegated Shopping Cart:** Imagine a shopping cart list (`<ul id="cart">`). Each item in the list contains a span with the item name and a button with the class `.add-item`. Use Event Delegation to listen for clicks on the entire `#cart` element, and log the name of the item to the console only when an `.add-item` button is clicked.
