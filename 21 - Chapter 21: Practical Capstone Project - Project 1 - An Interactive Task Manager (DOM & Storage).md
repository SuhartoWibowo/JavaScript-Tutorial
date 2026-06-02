# Part 7: Practical Capstone Projects

## Project 1: An Interactive Task Manager (DOM & Storage)

In the previous chapters, we covered individual components of JavaScript: variables, control flow, functions, objects, arrays, DOM manipulation, event handling, and client-side storage. 

Now, we will combine these concepts to build a fully functional, production-ready application from scratch: **An Interactive Task Manager**.

This project will apply:
*   **Dynamic DOM Manipulation** to update the user interface in real time.
*   **Event Delegation** to efficiently handle user actions (complete and delete) on dynamic list items.
*   **LocalStorage Integration** to persist tasks across page reloads.
*   **State Management** to track and filter tasks.

---

### Project Architecture
Our application will be split into three files:
1.  `index.html` (The markup structure)
2.  `style.css` (The visual layout)
3.  `app.js` (The interactive logic)

Let's build them step by step.

---

## Step 1: The HTML Structure (`index.html`)

Create a file named `index.html`. This markup establishes the container, the input form, a filtering toolbar, and the empty unordered list (`<ul>`) where our tasks will be dynamically injected.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dynamic Task Manager</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="app-container">
        <header>
            <h1>Task Manager</h1>
        </header>

        <!-- Form to add new tasks -->
        <form id="task-form">
            <input 
                type="text" 
                id="task-input" 
                placeholder="What needs to be done?" 
                required 
                autocomplete="off"
            >
            <button type="submit" id="add-btn">Add Task</button>
        </form>

        <!-- Filter Toolbar -->
        <div class="filter-toolbar">
            <span id="items-left">0 items left</span>
            <div class="filter-buttons">
                <button class="filter-btn active" data-filter="all">All</button>
                <button class="filter-btn" data-filter="active">Active</button>
                <button class="filter-btn" data-filter="completed">Completed</button>
            </div>
            <button id="clear-completed-btn">Clear Completed</button>
        </div>

        <!-- Dynamic List Container -->
        <ul id="task-list">
            <!-- JavaScript will inject tasks here -->
        </ul>
    </div>

    <script src="app.js"></script>
</body>
</html>
```

---

## Step 2: Visual Styling (`style.css`)

Create a file named `style.css`. These styles provide a clean interface, visual indications for completed items, and custom spacing.

```css
/* Reset basic page styles */
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
    background-color: #f4f6f8;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    min-height: 100vh;
    padding-top: 50px;
}

/* App Container */
.app-container {
    background: #ffffff;
    width: 100%;
    max-width: 550px;
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

header h1 {
    color: #333333;
    margin-bottom: 20px;
    text-align: center;
    font-size: 2rem;
}

/* Task Form */
#task-form {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

#task-input {
    flex-grow: 1;
    padding: 12px 16px;
    border: 2px solid #e2e8f0;
    border-radius: 8px;
    font-size: 1rem;
    transition: border-color 0.2s;
}

#task-input:focus {
    outline: none;
    border-color: #3182ce;
}

#add-btn {
    background-color: #3182ce;
    color: white;
    border: none;
    padding: 12px 20px;
    border-radius: 8px;
    font-size: 1rem;
    cursor: pointer;
    font-weight: 600;
    transition: background-color 0.2s;
}

#add-btn:hover {
    background-color: #2b6cb0;
}

/* Filter Toolbar */
.filter-toolbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 12px 0;
    border-bottom: 2px solid #f7fafc;
    color: #718096;
    font-size: 0.9rem;
    margin-bottom: 15px;
}

.filter-buttons {
    display: flex;
    gap: 5px;
}

.filter-btn, #clear-completed-btn {
    background: none;
    border: none;
    padding: 5px 10px;
    border-radius: 4px;
    cursor: pointer;
    color: #718096;
    font-weight: 500;
    transition: all 0.2s;
}

.filter-btn:hover, #clear-completed-btn:hover {
    color: #2d3748;
}

.filter-btn.active {
    background-color: #ebf8ff;
    color: #3182ce;
    font-weight: 600;
}

/* Task List */
#task-list {
    list-style: none;
}

.task-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 14px 10px;
    border-bottom: 1px solid #edf2f7;
    transition: background-color 0.1s;
}

.task-item:hover {
    background-color: #f7fafc;
}

.task-content {
    display: flex;
    align-items: center;
    gap: 12px;
    cursor: pointer;
}

.task-text {
    font-size: 1rem;
    color: #2d3748;
    transition: all 0.2s;
}

/* Completed State */
.task-item.completed .task-text {
    text-decoration: line-through;
    color: #a0aec0;
}

/* Delete Button */
.delete-btn {
    background: none;
    border: none;
    color: #e53e3e;
    font-weight: bold;
    cursor: pointer;
    padding: 5px 10px;
    border-radius: 4px;
    font-size: 1rem;
    transition: background-color 0.2s;
}

.delete-btn:hover {
    background-color: #fff5f5;
}
```

---

## Step 3: Interactive Application Logic (`app.js`)

Create a file named `app.js`. This script coordinates state tracking, DOM rendering, and LocalStorage reads/writes.

```javascript
// ==========================================
// 1. STATE MANAGEMENT
// ==========================================
// Core application state: An array of task objects
// Each task: { id: number, text: string, completed: boolean }
let tasks = [];

// Filter state: Tracks active view ('all', 'active', 'completed')
let currentFilter = 'all';

// ==========================================
// 2. DOM ELEMENT SELECTION
// ==========================================
const taskForm = document.getElementById('task-form');
const taskInput = document.getElementById('task-input');
const taskList = document.getElementById('task-list');
const itemsLeftSpan = document.getElementById('items-left');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clear-completed-btn');

// ==========================================
// 3. CORE FUNCTIONS (BUSINESS LOGIC)
// ==========================================

/**
 * Saves the current tasks state array to LocalStorage
 */
function saveTasksToStorage() {
    localStorage.setItem('tasks', JSON.stringify(tasks));
}

/**
 * Loads tasks state array from LocalStorage on app startup
 */
function loadTasksFromStorage() {
    const rawData = localStorage.getItem('tasks');
    try {
        tasks = rawData ? JSON.parse(rawData) : [];
    } catch (e) {
        console.error("Failed to load tasks. Resetting memory.");
        tasks = [];
    }
}

/**
 * Re-renders the entire tasks list UI based on current state and filters
 */
function renderUI() {
    // Clear out existing dynamic HTML items
    taskList.innerHTML = '';

    // Apply active filter rules to state array
    const filteredTasks = tasks.filter(task => {
        if (currentFilter === 'active') return !task.completed;
        if (currentFilter === 'completed') return task.completed;
        return true; // 'all'
    });

    // Generate dynamic list elements
    filteredTasks.forEach(task => {
        const li = document.createElement('li');
        li.className = `task-item ${task.completed ? 'completed' : ''}`;
        li.dataset.id = task.id; // Embed the ID into HTML markup

        li.innerHTML = `
            <div class="task-content">
                <input type="checkbox" class="toggle-checkbox" ${task.completed ? 'checked' : ''}>
                <span class="task-text">${escapeHTML(task.text)}</span>
            </div>
            <button class="delete-btn" aria-label="Delete task">&times;</button>
        `;

        taskList.appendChild(li);
    });

    // Update stats bar count (how many tasks remain uncompleted)
    const activeTasksCount = tasks.filter(task => !task.completed).length;
    itemsLeftSpan.textContent = `${activeTasksCount} item${activeTasksCount !== 1 ? 's' : ''} left`;
}

/**
 * Utility helper to prevent HTML injection attacks (XSS)
 * Converts special character entities into secure plain text format
 */
function escapeHTML(str) {
    const div = document.createElement('div');
    div.textContent = str;
    return div.innerHTML;
}

// ==========================================
// 4. ACTION EVENT HANDLERS
// ==========================================

/**
 * Handles form submission to create and add a new task
 */
function handleAddTask(event) {
    event.preventDefault(); // Stop standard browser page reload
    
    const taskText = taskInput.value.trim();
    if (taskText === '') return;

    // Build task object model
    const newTask = {
        id: Date.now(), // Unique identifier based on execution timestamp
        text: taskText,
        completed: false
    };

    // Update state and UI
    tasks.push(newTask);
    saveTasksToStorage();
    renderUI();

    // Clear and refocus input
    taskInput.value = '';
    taskInput.focus();
}

/**
 * Handles user interactions within the list container using EVENT DELEGATION
 */
function handleListInteraction(event) {
    const target = event.target;
    // Walk up elements to locate containing LI card to retrieve data ID
    const taskItem = target.closest('.task-item');
    if (!taskItem) return;

    const taskId = Number(taskItem.dataset.id);

    // Scenario A: User clicked checkbox or surrounding text content to toggle complete state
    if (target.classList.contains('toggle-checkbox') || target.closest('.task-content')) {
        tasks = tasks.map(task => {
            if (task.id === taskId) {
                return { ...task, completed: !task.completed };
            }
            return task;
        });
        saveTasksToStorage();
        renderUI();
    }

    // Scenario B: User clicked delete button to remove task completely
    if (target.classList.contains('delete-btn')) {
        tasks = tasks.filter(task => task.id !== taskId);
        saveTasksToStorage();
        renderUI();
    }
}

/**
 * Handles switching active visual filters and rebuilding list
 */
function handleFilterChange(event) {
    const button = event.target;
    if (!button.classList.contains('filter-btn')) return;

    // Adjust active visual state class
    filterBtns.forEach(btn => btn.classList.remove('active'));
    button.classList.add('active');

    // Update state filter rule and render
    currentFilter = button.dataset.filter;
    renderUI();
}

/**
 * Clears all completed tasks from state array entirely
 */
function handleClearCompleted() {
    tasks = tasks.filter(task => !task.completed);
    saveTasksToStorage();
    renderUI();
}

// ==========================================
// 5. APPLICATION INITIALIZATION
// ==========================================
function initApp() {
    // 1. Load initial storage
    loadTasksFromStorage();

    // 2. Bind event listeners
    taskForm.addEventListener('submit', handleAddTask);
    taskList.addEventListener('click', handleListInteraction);
    document.querySelector('.filter-buttons').addEventListener('click', handleFilterChange);
    clearCompletedBtn.addEventListener('click', handleClearCompleted);

    // 3. Perform initial screen render
    renderUI();
}

// Kick off app execution once the DOM has loaded
document.addEventListener('DOMContentLoaded', initApp);
```

---

### Project Review

By completing this project, you have integrated several key JavaScript programming concepts:

1.  **State-Driven Design:** The HTML code does not update the page directly. Instead, your actions edit the *source of truth* (the `tasks` state array), and then the `renderUI()` function synchronizes the DOM visual presentation to reflect that state.
2.  **Robust Event Delegation:** We set a single event listener on the parent `<ul>` element (`taskList`). This detects clicks on present and future list items, checking classes to determine whether to toggle completion or delete the task.
3.  **Basic Input Sanitization:** Using our utility helper function `escapeHTML()` to convert potential tag markup ensures that malicious users cannot inject custom scripts into your dynamic page structure.
