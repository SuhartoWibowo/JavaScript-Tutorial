# Chapter 14: Document Object Model (DOM) Basics

Up to this point, we have primarily executed JavaScript within the terminal or logged output to the browser’s developer console. In this section—**Part 5: JavaScript in the Browser**—we will explore how JavaScript is used to make static web pages dynamic and interactive.

The bridge that allows JavaScript to communicate with, read, and modify an HTML document is called the **Document Object Model (DOM)**. In this chapter, we will learn what the DOM is, how to select specific elements from a page, and how to create, modify, or delete HTML elements dynamically.

---

## 14.1 What is the DOM?

When a browser loads an HTML document, it reads the raw text and parses it into a structured, object-oriented representation in computer memory. This representation is called the **DOM**.

The DOM represents the web page as a **tree of objects** (often called nodes). At the absolute top of this tree is the global `document` object. Every HTML tag, attribute, and text snippet becomes a branch or "leaf" in the tree.

```
                  [ document ]
                       |
                     <html>
                   /        \
             <head>          <body>
               |             /    \
            <title>        <h1>   <p>
```

By interacting with the `document` object, JavaScript can inspect, add, alter, or remove elements of the page in real time without requiring the user to reload the web page.

---

## 14.2 Selecting DOM Elements

Before you can change an element on a web page, you must first tell JavaScript how to find it. The `document` object provides several methods for selecting elements.

### Modern Selection Methods (Recommended)
Modern web development relies heavily on CSS selectors. JavaScript offers two highly versatile selection methods that accept standard CSS selectors:

#### 1. `document.querySelector(selector)`
Returns the **first** element on the page that matches the specified CSS selector. If no matching element is found, it returns `null`.

```javascript
// Selecting by tag name
const heading = document.querySelector("h1");

// Selecting by ID (using #)
const submitButton = document.querySelector("#submit-btn");

// Selecting by Class name (using .)
const firstAlert = document.querySelector(".alert-message");
```

#### 2. `document.querySelectorAll(selector)`
Returns **all** elements on the page that match the specified CSS selector, packaged inside an array-like collection called a `NodeList`. You can iterate through a `NodeList` using standard loop methods like `.forEach()`.

```javascript
const allParagraphs = document.querySelectorAll("p");

allParagraphs.forEach((paragraph, index) => {
    console.log(`Paragraph ${index}: ${paragraph.textContent}`);
});
```

### Legacy Selection Methods
You will also encounter older, highly specific selection methods in existing codebases:
*   `document.getElementById("id")`: Highly efficient method to select a single element by its ID (do not use `#` here).
*   `document.getElementsByClassName("className")`: Returns an HTMLCollection of all elements with a specific class name.
*   `document.getElementsByTagName("tagName")`: Returns an HTMLCollection of all elements matching an HTML tag.

---

## 14.3 Modifying Elements

Once you have selected an element, you can modify its content, attributes, and styles.

### 1. Modifying Text and HTML Content
To update what is displayed inside an element, you use `textContent` or `innerHTML`.

```javascript
const banner = document.querySelector(".banner");

// textContent: Modifies raw text safely (ignores HTML tags)
banner.textContent = "Welcome to our updated website!";

// innerHTML: Parses text as active HTML (allows inserting tags)
banner.innerHTML = "<strong>Welcome!</strong> Please log in.";
```
*Security Note:* Avoid using `innerHTML` if you are injecting untrusted data from a user (like form inputs). Doing so can expose your application to Cross-Site Scripting (XSS) attacks. For safe plain-text injection, always default to `textContent`.

### 2. Modifying Attributes
You can read, write, or remove HTML attributes (such as `src`, `href`, or `disabled`) using specialized methods:

```javascript
const userImage = document.querySelector("#user-profile");

// setAttribute(name, value)
userImage.setAttribute("src", "images/profile-new.jpg");

// getAttribute(name)
const imagePath = userImage.getAttribute("src"); 

// removeAttribute(name)
userImage.removeAttribute("alt");
```

### 3. Modifying Styles
You can alter CSS presentation in two ways:

#### A. Direct Inline Styles (via `.style`)
You can target CSS properties directly. Property names are written in **camelCase** instead of standard CSS kebab-case (e.g., `background-color` becomes `backgroundColor`).

```javascript
const card = document.querySelector(".card");
card.style.backgroundColor = "lightgray";
card.style.border = "1px solid black";
```

#### B. Modifying Classes (Recommended)
Modifying inline styles directly can clutter your JavaScript. It is usually cleaner to define styles in your external CSS file and use JavaScript's `classList` property to toggle those CSS classes on and off.

```javascript
const notification = document.querySelector("#banner-notification");

notification.classList.add("hidden");    // Adds class
notification.classList.remove("hidden"); // Removes class
notification.classList.toggle("active"); // Toggles class (adds if missing, removes if present)
```

---

## 14.4 Creating, Inserting, and Deleting Elements

You are not limited to modifying existing HTML; you can build entirely new nodes and insert them anywhere in the DOM.

### 1. Creating Elements
To create a new HTML node, use `document.createElement()` and assign its properties. This element initially exists only in JavaScript memory, not yet attached to the page structure.

```javascript
// Step 1: Create the element
const newListItem = document.createElement("li");

// Step 2: Configure its content and styling
newListItem.textContent = "Learn DOM Manipulation";
newListItem.classList.add("todo-item");
```

### 2. Inserting Elements
To display the element on the web page, you must append it to a parent element already existing in the DOM tree.

```javascript
const todoList = document.querySelector("#todo-list");

// Append: Inserts the new node as the last child of the parent
todoList.appendChild(newListItem);

// Prepend: Inserts the new node as the first child of the parent
// todoList.prepend(newListItem);
```

### 3. Deleting Elements
To remove an element from the page, you can use the modern `.remove()` method directly on the target element.

```javascript
const completedItem = document.querySelector(".completed");

// Removes the element directly from the page
completedItem.remove();
```

---

## Exercises and Review

Practice interacting with elements on a web page:

1. **The Selector Query:** Write a JavaScript expression that selects the third item inside an unordered list with an ID of `#navigation-menu`.
2. **The Dynamic Greeter:** Imagine you have a paragraph in your HTML: `<p id="greeter">Hello Guest</p>`. Write JavaScript code that:
   * Selects this paragraph.
   * Prompts the user for their name (or uses a default value).
   * Changes the text content of the paragraph to read: *"Welcome, [user's name]!"*.
   * Toggles a CSS class called `highlight` on the paragraph.
3. **The List Builder:** Write a JavaScript script that creates an array of three favorite movies. Iterate through the array and programmatically build a bulleted list (`<ul>` containing `<li>` tags) inside a div with the class `.movie-container`, appending each movie as an individual list element.
