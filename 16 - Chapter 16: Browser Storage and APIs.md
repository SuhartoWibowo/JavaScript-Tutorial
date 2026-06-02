# Chapter 16: Browser Storage and APIs

When you refresh a web page or close a browser tab, your JavaScript execution environment resets. Any active variables, configurations, or application states stored in memory are instantly wiped out.

To build applications that remember user state—such as saving user preferences (e.g., dark mode), retaining a shopping cart between visits, or keeping a user logged in—the browser provides client-side storage mechanisms. In this chapter, we will learn how to store data on the user's device using **Web Storage** and **Cookies**, and we will look at modern browser APIs like **Geolocation** and **Intersection Observer**.

---

## 16.1 Web Storage: `localStorage` and `sessionStorage`

The Web Storage API provides a simple, synchronous mechanism to store key-value pairs locally in the browser. 

There are two types of Web Storage:
1.  **`localStorage`:** Stores data with no expiration date. The data remains saved on the user's device even if they close the browser tab, exit the browser, or restart their computer.
2.  **`sessionStorage`:** Stores data only for the duration of the page session. The data is cleared as soon as the specific browser tab or window is closed.

Both storage mechanisms share the exact same methods, are limited to storing roughly 5MB of data per origin, and **only store data as strings**.

### Common Methods
```javascript
// 1. Saving data: setItem(key, value)
localStorage.setItem("theme", "dark");

// 2. Reading data: getItem(key)
const currentTheme = localStorage.getItem("theme");
console.log(currentTheme); // Outputs: "dark"

// 3. Removing a single item: removeItem(key)
localStorage.removeItem("theme");

// 4. Clearing all storage for the origin: clear()
localStorage.clear();
```

### Storing Complex Data: Objects and Arrays
Because Web Storage only accepts strings, trying to save an object directly will result in the string `"[object Object]"`, corrupting your data. To solve this, serialize the data using JSON:

```javascript
const userProfile = {
    username: "dev_alice",
    score: 1500
};

// Convert to string before saving
localStorage.setItem("user", JSON.stringify(userProfile));

// Parse back into an object when reading
const savedUserRaw = localStorage.getItem("user");
const savedUserObj = JSON.parse(savedUserRaw);

console.log(savedUserObj.username); // Outputs: "dev_alice"
```
*Security Warning:* Never store sensitive information like passwords, credit card numbers, or authentication tokens in `localStorage`. Because it is accessible via JavaScript, any script running on your page (including third-party tracking scripts or scripts injected via XSS attacks) can read this data.

---

## 16.2 Storing Data with Cookies

**Cookies** are the oldest client-side storage mechanism. Unlike Web Storage, cookies are automatically sent back and forth between the browser and the server with every single HTTP request. This makes them ideal for managing login sessions and authentication.

### Basic Syntax
Reading and writing cookies in JavaScript uses the `document.cookie` property. Its API is older and less intuitive than Web Storage.

```javascript
// Writing a cookie
document.cookie = "username=Bob; expires=Fri, 31 Dec 2027 12:00:00 UTC; path=/";

// Reading cookies returns a single string containing all cookies separated by semicolons
console.log(document.cookie); // Outputs: "theme=dark; username=Bob"
```

### Cookie Attributes and Security
Due to their role in authentication, cookies have specialized security flags that are typically set by the backend server:
*   **`Secure`:** Ensures the cookie is only transmitted over encrypted (HTTPS) connections.
*   **`HttpOnly`:** Prevents client-side JavaScript from reading the cookie via `document.cookie`. This is a vital defense against XSS attacks targeting session tokens.
*   **`SameSite`:** Controls whether cookies are sent with cross-site requests, protecting users from Cross-Site Request Forgery (CSRF) attacks.

---

## 16.3 Modern Browser APIs

Modern web browsers are capable of interacting with hardware, operating systems, and page layouts through advanced APIs.

### 1. The Geolocation API
The Geolocation API allows web applications to request the user's physical geographic coordinates (latitude and longitude). For privacy reasons, the browser will always ask the user for permission first.

```javascript
function findUserLocation() {
    if (!navigator.geolocation) {
        console.warn("Geolocation is not supported by your browser.");
        return;
    }

    navigator.geolocation.getCurrentPosition(
        (position) => {
            const { latitude, longitude } = position.coords;
            console.log(`Latitude: ${latitude}, Longitude: ${longitude}`);
        },
        (error) => {
            console.error(`Error retrieving location: ${error.message}`);
        }
    );
}

// findUserLocation();
```

### 2. The Intersection Observer API
The Intersection Observer API provides an efficient way to detect when an HTML element enters or leaves the visible browser viewport. It is commonly used to implement lazy-loading images, endless scrolling feeds, and triggering scroll animations.

Unlike scroll event listeners (which fire constantly and can lag the page), the Intersection Observer is optimized to run asynchronously on the browser's rendering thread.

```javascript
// Step 1: Create the callback function
const observerCallback = (entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            console.log("Element is now visible on screen!");
            // You can load an image here or trigger an animation, 
            // then stop observing the element
            observer.unobserve(entry.target);
        }
    });
};

// Step 2: Instantiate the observer
const observer = new IntersectionObserver(observerCallback);

// Step 3: Target an element and start observing it
const footerElement = document.querySelector("footer");
observer.observe(footerElement);
```

---

## Exercises and Review

Put client storage and browser APIs into practice with these challenges:

1. **The Preference Manager:** Write a script that checks `localStorage` for a key named `theme`. 
   * If the key does not exist, set it to `"light"` by default.
   * If it does exist, apply its value as a class name (e.g., `.light` or `.dark`) to the page's `<body>` element.
   * Create a button on the page that, when clicked, toggles the value in `localStorage` between `"light"` and `"dark"`, and updates the `<body>` class instantly.
2. **JSON Safe Loader:** Write a function `loadFromStorage(key)` that reads a value from `localStorage`. Use a `try...catch` block to safely parse the output with `JSON.parse()`. If parsing fails (for example, if the stored data is a plain string instead of JSON format), handle the error gracefully and return the raw string or a fallback value.
3. **The Lazy Loader Simulator:** Write a script using `IntersectionObserver` that targets an image tag with a placeholder source (`<img data-src="actual-image.jpg" src="placeholder.gif">`). When the image enters the viewport, replace its `src` attribute with the path stored in its `data-src` attribute, then unobserve the element.
