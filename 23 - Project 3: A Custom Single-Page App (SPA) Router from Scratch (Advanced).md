## Project 3: A Custom Single-Page App (SPA) Router from Scratch (Advanced)

In traditional multi-page web applications, clicking a link triggers a request to a backend server. The server generates a brand-new HTML page, sends it back over the network, and the browser performs a full-page reload. This process can cause visual flickering and slower transitions.

Modern web applications often use a **Single-Page Application (SPA)** architecture. In an SPA, the browser loads a single HTML file once. When a user navigates to a different page, JavaScript intercepts the action, prevents the browser from loading a new page, updates the URL in the address bar silently, and swaps out the content in the main container dynamically.

In this advanced project, we will build a **custom SPA routing system from scratch** without using popular frameworks like React, Vue, or Angular.

### Skills Applied
*   The HTML5 **History API** (`pushState` and the `popstate` event) to manage URL changes.
*   **ES6 Modules** to decouple routes, components, and router engines.
*   Advanced **Event Interception** and Delegation.
*   Handling programmatic state transitions (e.g., updating navigation link styling dynamically).

---

### Project Architecture
Our codebase will be organized modularly:
1.  `index.html` (The primary application frame and navbar)
2.  `style.css` (The presentation layout and active link animations)
3.  `views.js` (An object dictionary containing functions that return dynamic HTML views)
4.  `router.js` (The router engine class)
5.  `app.js` (The entry script initializing the application)

---

## Step 1: The Single-Page Shell (`index.html`)

Create a file named `index.html`. This page contains a persistent navigation bar and a single target mount element (`<div id="app-root">`) where different views will be loaded.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SPA Router from Scratch</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <!-- Persistent Layout Frame -->
    <div class="app-layout">
        <nav class="navbar">
            <div class="logo">AppShell</div>
            <div class="nav-links">
                <!-- data-link alerts our router to intercept this click -->
                <a href="/" class="nav-item" data-link>Home</a>
                <a href="/dashboard" class="nav-item" data-link>Dashboard</a>
                <a href="/about" class="nav-item" data-link>About</a>
            </div>
        </nav>

        <!-- View Mounting Element -->
        <main id="app-root">
            <!-- Dynamic components inject here -->
        </main>
    </div>

    <!-- Since we are using ES Modules, type="module" is mandatory -->
    <script type="module" src="app.js"></script>
</body>
</html>
```

---

## Step 2: Visual Presentation (`style.css`)

Create a file named `style.css`. These styles provide simple formatting for our layouts, cards, and transitions.

```css
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: system-ui, -apple-system, sans-serif;
}

body {
    background-color: #f7fafc;
    color: #2d3748;
}

.app-layout {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
}

/* Navbar */
.navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: #ffffff;
    padding: 15px 30px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
}

.logo {
    font-size: 1.3rem;
    font-weight: 700;
    color: #3182ce;
}

.nav-links {
    display: flex;
    gap: 20px;
}

.nav-item {
    color: #4a5568;
    text-decoration: none;
    font-weight: 500;
    padding: 8px 12px;
    border-radius: 6px;
    transition: all 0.2s;
}

.nav-item:hover {
    color: #3182ce;
    background-color: #ebf8ff;
}

/* Custom Visual State applied by our Router */
.nav-item.active {
    color: #ffffff;
    background-color: #3182ce;
}

/* Content Container Mount */
#app-root {
    flex-grow: 1;
    max-width: 800px;
    width: 100%;
    margin: 0 auto;
    padding: 40px 20px;
}

/* Views Styles */
.view-card {
    background-color: #ffffff;
    padding: 40px;
    border-radius: 12px;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.02);
    animation: slideUp 0.3s ease-out;
}

@keyframes slideUp {
    from { opacity: 0; transform: translateY(15px); }
    to { opacity: 1; transform: translateY(0); }
}

.view-card h2 {
    font-size: 2rem;
    margin-bottom: 15px;
    color: #1a202c;
}

.view-card p {
    color: #4a5568;
    line-height: 1.6;
    margin-bottom: 20px;
}

.action-btn {
    background-color: #3182ce;
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 6px;
    cursor: pointer;
    font-weight: 600;
}
```

---

## Step 3: Page Component Views (`views.js`)

Create a file named `views.js`. Instead of requesting separate HTML files over the network, our view components are defined inside JS functions that return dynamic HTML strings.

```javascript
// file: views.js

export const views = {
    home: () => `
        <div class="view-card">
            <h2>Welcome to the Home Page</h2>
            <p>This is a custom Single-Page Application (SPA) routing architecture constructed completely from scratch using native vanilla JavaScript.</p>
            <p>Observe that navigating across menu links refreshes page elements near-instantaneously without any network flickering or full page updates.</p>
        </div>
    `,
    
    dashboard: () => `
        <div class="view-card">
            <h2>Dashboard Analytics</h2>
            <p>This section represents a dynamic dashboard. In a real-world application, this is where you would trigger API fetch requests to retrieve charts and user datasets.</p>
            <button class="action-btn" id="dashboard-alert-btn">Generate Security Token</button>
        </div>
    `,
    
    about: () => `
        <div class="view-card">
            <h2>About Our Architecture</h2>
            <p>Traditional routers require server coordination to compile templates. This engine intercepts user navigation directly using the HTML5 History API to provide a smooth, responsive desktop application experience.</p>
        </div>
    `,
    
    notFound: () => `
        <div class="view-card" style="border-left: 5px solid #e53e3e;">
            <h2>404 - Page Not Found</h2>
            <p>The route you specified does not exist. Please check the address bar or return to the main homepage.</p>
        </div>
    `
};
```

---

## Step 4: The Custom Routing Engine (`router.js`)

Create a file named `router.js`. This module contains our router engine. It manages the registration of valid paths, monitors user navigation actions, prevents page reloads, updates the active tab styling, and mounts views dynamically.

```javascript
// file: router.js

export class Router {
    /**
     * Initializes the router configurations
     * @param {HTMLElement} rootElement - The DOM container where views are loaded.
     * @param {Array} routes - List of registered routes matching paths to view names.
     */
    constructor(rootElement, routes) {
        this.rootElement = rootElement;
        this.routes = routes;
        
        // Bind functions to preserve 'this' execution context inside event listeners
        this.handlePopState = this.handlePopState.bind(this);
        this.handleLinkClick = this.handleLinkClick.bind(this);
    }

    /**
     * Translates the current URL path into a matching registered view component
     */
    navigateTo(path) {
        // Step 1: Update the browser's address bar without reloading the page
        // history.pushState(state, unused, url)
        window.history.pushState(null, null, path);

        // Step 2: Resolve and render the view for the new path
        this.resolveRoute();
    }

    /**
     * Finds the route corresponding to the current window location path
     */
    resolveRoute() {
        const currentPath = window.location.pathname;

        // Try to match current URL path with registered paths
        const matchedRoute = this.routes.find(route => route.path === currentPath);

        if (matchedRoute) {
            // Render the matched view component inside the root container
            this.rootElement.innerHTML = matchedRoute.view();
        } else {
            // Fallback to 404 View
            const fallbackRoute = this.routes.find(route => route.path === "*");
            this.rootElement.innerHTML = fallbackRoute 
                ? fallbackRoute.view() 
                : "<h2>Page Not Found</h2>";
        }

        // Keep navigation bar styling synchronized with active route
        this.updateNavbarLinks(currentPath);
        
        // Execute dynamic bindings inside freshly injected DOM components
        this.bindViewEvents(currentPath);
    }

    /**
     * Syncs visual "active" class states across link navigation elements
     */
    updateNavbarLinks(currentPath) {
        const navLinks = document.querySelectorAll("[data-link]");
        navLinks.forEach(link => {
            const pathAttribute = link.getAttribute("href");
            
            // Highlight the active page link
            if (pathAttribute === currentPath) {
                link.classList.add("active");
            } else {
                link.classList.remove("active");
            }
        });
    }

    /**
     * Attaches interactive JS events dynamically to newly injected view elements
     */
    bindViewEvents(path) {
        if (path === "/dashboard") {
            const tokenBtn = document.getElementById("dashboard-alert-btn");
            if (tokenBtn) {
                tokenBtn.addEventListener("click", () => {
                    alert(`Secure App Token: ${Math.random().toString(36).substr(2, 9).toUpperCase()}`);
                });
            }
        }
    }

    /**
     * Prevents default full page loads and redirects programmatic flow
     */
    handleLinkClick(event) {
        // Intercept clicks on links that have the [data-link] attribute
        const link = event.target.closest("[data-link]");
        if (link) {
            event.preventDefault(); // Stop standard browser request
            const path = link.getAttribute("href");
            this.navigateTo(path);
        }
    }

    /**
     * Triggers when browser navigation buttons are pressed (back or forward)
     */
    handlePopState() {
        this.resolveRoute();
    }

    /**
     * Attaches persistent global listener bindings on startup
     */
    start() {
        // Intercept link clicks across the entire layout container
        document.body.addEventListener("click", this.handleLinkClick);

        // Listen for when users click the browser's Back or Forward buttons
        window.addEventListener("popstate", this.handlePopState);

        // Resolve the initial route based on the URL the page was loaded with
        this.resolveRoute();
    }

    /**
     * Cleans up listeners when stopping or unmounting the router
     */
    stop() {
        document.body.removeEventListener("click", this.handleLinkClick);
        window.removeEventListener("popstate", this.handlePopState);
    }
}
```

---

## Step 5: Application Initialization (`app.js`)

Create a file named `app.js`. This script acts as our application entry point. It imports the views and the router engine, defines the routing mapping table, instantiates the router, and starts the application.

```javascript
// file: app.js

import { Router } from "./router.js";
import { views } from "./views.js";

// 1. Locate the mounting node element in our DOM
const rootContainer = document.getElementById("app-root");

// 2. Define our routing table, mapping paths to imported view functions
const routes = [
    { path: "/", view: views.home },
    { path: "/dashboard", view: views.dashboard },
    { path: "/about", view: views.about },
    { path: "*", view: views.notFound } // Fallback match
];

// 3. Instantiate the routing engine
const appRouter = new Router(rootContainer, routes);

// 4. Start the router
appRouter.start();
```

---

### Project Review

By completing this advanced SPA project, you have combined several key client-side concepts:

1.  **Manipulating the History API:** Standard browsers navigate away on click actions. By calling `event.preventDefault()`, we stopped this default behavior. We then used `window.history.pushState` to update the URL in the address bar silently, maintaining correct back-and-forward browser navigation support.
2.  **Listening for `popstate`:** If a user clicks the browser's native **Back** or **Forward** buttons, the address bar changes, but the document does not refresh. By binding to the `popstate` event, the router detects this browser action and resolves the correct view automatically.
3.  **Dynamic DOM Event Binding:** When views are compiled dynamically as HTML strings (`views.js`), elements like buttons do not exist in the DOM when the application first starts. We resolved this by including a lifecycle hook (`bindViewEvents`) inside our routing engine that attaches click events immediately after the new HTML views are rendered.
4.  **Namespace Structuring with ES Modules:** This project is structured similarly to modern frontend framework code. We kept our view templates, router classes, and bootstrap code in separate files, exporting and importing them only where they are needed.
