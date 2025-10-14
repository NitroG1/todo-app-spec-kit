# Function Interfaces: Todo List Application

**Feature**: Todo List Application
**Date**: 2025-10-11
**Type**: Internal JavaScript Function Contracts

## Overview

This document defines the function interfaces (contracts) for the todo list application. Since this is a client-side-only application with no REST API, the "contracts" are internal JavaScript function signatures that define how different parts of the application interact.

---

## Core Functions

### Task Management

#### `addTask(taskText)`

**Purpose**: Create a new task and add it to the task list

**Parameters**:
- `taskText` (string): The task description entered by user

**Returns**: `void`

**Side Effects**:
- Validates input text
- Creates new Task object
- Adds task to global `tasks` array
- Saves to localStorage
- Triggers UI re-render
- Clears input field
- Announces addition to screen readers

**Validation**:
- Text must not be empty after trimming
- Text must be 1-1000 characters

**Example**:
```javascript
addTask('Buy groceries');
// Result: New task added with id, text, completed: false
```

---

#### `deleteTask(taskId)`

**Purpose**: Remove a task from the task list

**Parameters**:
- `taskId` (number): Unique identifier of task to delete

**Returns**: `void`

**Side Effects**:
- Finds task by ID
- Removes from `tasks` array
- Saves to localStorage
- Triggers UI re-render
- Announces removal to screen readers

**Example**:
```javascript
deleteTask(1697123456789);
// Result: Task with matching ID removed
```

---

#### `toggleTaskComplete(taskId)`

**Purpose**: Toggle the completion status of a task

**Parameters**:
- `taskId` (number): Unique identifier of task to toggle

**Returns**: `void`

**Side Effects**:
- Finds task by ID
- Toggles `completed` property (true ↔ false)
- Saves to localStorage
- Triggers UI re-render (updates visual state)

**Example**:
```javascript
toggleTaskComplete(1697123456789);
// Result: Task's completed status flipped
```

---

### Storage Functions

#### `saveTasks()`

**Purpose**: Persist current tasks array to localStorage

**Parameters**: None

**Returns**: `void`

**Side Effects**:
- Serializes `tasks` array to JSON
- Writes to localStorage key `todoAppTasks`
- Handles quota exceeded errors

**Error Handling**:
- Catches `QuotaExceededError` and alerts user
- Logs other errors to console

**Example**:
```javascript
saveTasks();
// Result: tasks array saved to localStorage
```

---

#### `loadTasks()`

**Purpose**: Load tasks from localStorage into memory

**Parameters**: None

**Returns**: `Task[]` (array of Task objects)

**Side Effects**:
- Reads from localStorage key `todoAppTasks`
- Parses JSON string
- Validates data structure
- Returns empty array if no data or error

**Error Handling**:
- Returns `[]` if localStorage unavailable
- Returns `[]` if parse error
- Filters out invalid task objects

**Example**:
```javascript
const tasks = loadTasks();
// Result: Array of Task objects or []
```

---

#### `saveFilter()`

**Purpose**: Persist current filter selection to localStorage

**Parameters**: None

**Returns**: `void`

**Side Effects**:
- Writes `currentFilter` to localStorage key `todoAppFilter`

**Example**:
```javascript
saveFilter();
// Result: Current filter saved
```

---

#### `loadFilter()`

**Purpose**: Load filter selection from localStorage

**Parameters**: None

**Returns**: `string` (filter value: 'all', 'active', or 'completed')

**Side Effects**:
- Reads from localStorage key `todoAppFilter`
- Returns 'all' as default if not found

**Example**:
```javascript
const filter = loadFilter();
// Result: 'all', 'active', or 'completed'
```

---

### Filter Functions

#### `setFilter(filterValue)`

**Purpose**: Change the current filter and update UI

**Parameters**:
- `filterValue` (string): 'all', 'active', or 'completed'

**Returns**: `void`

**Side Effects**:
- Updates global `currentFilter` variable
- Saves filter to localStorage
- Triggers UI re-render with filtered tasks
- Updates filter button active states

**Validation**:
- Only accepts 'all', 'active', 'completed'

**Example**:
```javascript
setFilter('active');
// Result: Only incomplete tasks displayed
```

---

#### `getFilteredTasks()`

**Purpose**: Get tasks matching current filter

**Parameters**: None

**Returns**: `Task[]` (filtered array of Task objects)

**Side Effects**: None (pure function)

**Logic**:
- If `currentFilter === 'all'`: return all tasks
- If `currentFilter === 'active'`: return tasks where `completed === false`
- If `currentFilter === 'completed'`: return tasks where `completed === true`

**Example**:
```javascript
const filtered = getFilteredTasks();
// Result: Array of tasks matching current filter
```

---

### Rendering Functions

#### `renderTasks()`

**Purpose**: Update the DOM to display current filtered tasks

**Parameters**: None

**Returns**: `void`

**Side Effects**:
- Clears current task list DOM
- Gets filtered tasks via `getFilteredTasks()`
- Creates DOM elements for each task
- Attaches event listeners
- Updates ARIA live region

**DOM Updates**:
- Checkbox for each task (with completion state)
- Task text (with strikethrough if completed)
- Delete button (with aria-label)

**Example**:
```javascript
renderTasks();
// Result: Task list DOM updated
```

---

#### `renderFilterButtons()`

**Purpose**: Update filter button active states

**Parameters**: None

**Returns**: `void`

**Side Effects**:
- Updates button classes to show active filter
- Ensures only current filter button has 'active' class

**Example**:
```javascript
renderFilterButtons();
// Result: Active filter button highlighted
```

---

### Initialization Functions

#### `init()`

**Purpose**: Initialize application on page load

**Parameters**: None

**Returns**: `void`

**Side Effects**:
- Checks localStorage availability
- Loads tasks from localStorage
- Loads filter from localStorage
- Sets up event listeners (form submit, filter buttons)
- Renders initial UI

**Called**: Once on `DOMContentLoaded` event

**Example**:
```javascript
document.addEventListener('DOMContentLoaded', init);
```

---

### Utility Functions

#### `generateTaskId()`

**Purpose**: Generate unique identifier for new task

**Parameters**: None

**Returns**: `number` (timestamp-based ID)

**Side Effects**: None (pure function)

**Implementation**:
```javascript
function generateTaskId() {
  return Date.now();
}
```

---

#### `isValidTaskText(text)`

**Purpose**: Validate task text input

**Parameters**:
- `text` (string): Text to validate

**Returns**: `boolean` (true if valid, false otherwise)

**Side Effects**: None (pure function)

**Validation Rules**:
- Not empty after trimming
- Length between 1 and 1000 characters

**Example**:
```javascript
isValidTaskText('  '); // false (empty after trim)
isValidTaskText('Buy groceries'); // true
```

---

#### `escapeHTML(text)`

**Purpose**: Escape HTML characters to prevent XSS

**Parameters**:
- `text` (string): Text to escape

**Returns**: `string` (escaped text)

**Side Effects**: None (pure function)

**Escapes**:
- `<` → `&lt;`
- `>` → `&gt;`
- `&` → `&amp;`
- `"` → `&quot;`
- `'` → `&#039;`

**Example**:
```javascript
escapeHTML('<script>alert("xss")</script>');
// Returns: '&lt;script&gt;alert(&quot;xss&quot;)&lt;/script&gt;'
```

---

## Event Handlers

### `handleFormSubmit(event)`

**Purpose**: Handle task form submission

**Parameters**:
- `event` (Event): Form submit event

**Returns**: `void`

**Side Effects**:
- Prevents default form submission
- Gets input value
- Validates input
- Calls `addTask()` if valid
- Focuses input field

**Example**:
```javascript
formElement.addEventListener('submit', handleFormSubmit);
```

---

### `handleCheckboxChange(taskId)`

**Purpose**: Handle checkbox click for task completion

**Parameters**:
- `taskId` (number): ID of task to toggle

**Returns**: `void`

**Side Effects**:
- Calls `toggleTaskComplete(taskId)`

**Example**:
```javascript
checkbox.addEventListener('change', () => handleCheckboxChange(task.id));
```

---

### `handleDeleteClick(taskId)`

**Purpose**: Handle delete button click

**Parameters**:
- `taskId` (number): ID of task to delete

**Returns**: `void`

**Side Effects**:
- Calls `deleteTask(taskId)`

**Example**:
```javascript
deleteBtn.addEventListener('click', () => handleDeleteClick(task.id));
```

---

### `handleFilterClick(filterValue)`

**Purpose**: Handle filter button click

**Parameters**:
- `filterValue` (string): Filter to apply ('all', 'active', 'completed')

**Returns**: `void`

**Side Effects**:
- Calls `setFilter(filterValue)`

**Example**:
```javascript
filterBtn.addEventListener('click', () => handleFilterClick('active'));
```

---

## Function Call Flow

### Add Task Flow
```
User submits form
  ↓
handleFormSubmit(event)
  ↓
isValidTaskText(text) [validation]
  ↓
addTask(taskText)
  ↓
generateTaskId() [create ID]
  ↓
tasks.push(newTask) [update state]
  ↓
saveTasks() [persist]
  ↓
renderTasks() [update UI]
```

### Toggle Complete Flow
```
User clicks checkbox
  ↓
handleCheckboxChange(taskId)
  ↓
toggleTaskComplete(taskId)
  ↓
Find task in array, toggle completed
  ↓
saveTasks() [persist]
  ↓
renderTasks() [update UI]
```

### Delete Task Flow
```
User clicks delete button
  ↓
handleDeleteClick(taskId)
  ↓
deleteTask(taskId)
  ↓
Remove from tasks array
  ↓
saveTasks() [persist]
  ↓
renderTasks() [update UI]
```

### Change Filter Flow
```
User clicks filter button
  ↓
handleFilterClick(filterValue)
  ↓
setFilter(filterValue)
  ↓
Update currentFilter variable
  ↓
saveFilter() [persist]
  ↓
renderTasks() [re-render with filter]
  ↓
renderFilterButtons() [update active state]
```

### Page Load Flow
```
Page loads, DOMContentLoaded fires
  ↓
init()
  ↓
loadTasks() → populate tasks array
  ↓
loadFilter() → set currentFilter
  ↓
Setup event listeners
  ↓
renderTasks() [initial render]
  ↓
renderFilterButtons() [initial state]
```

---

## Type Definitions (for reference)

```javascript
/**
 * Task Object
 * @typedef {Object} Task
 * @property {number} id - Unique identifier (timestamp)
 * @property {string} text - Task description
 * @property {boolean} completed - Completion status
 */

/**
 * Filter Values
 * @typedef {'all'|'active'|'completed'} FilterValue
 */
```

---

## Function Organization in Code

Recommended organization in the single JavaScript file:

```javascript
// 1. State Variables
let tasks = [];
let currentFilter = 'all';

// 2. Utility Functions
function generateTaskId() { ... }
function isValidTaskText(text) { ... }
function escapeHTML(text) { ... }

// 3. Storage Functions
function saveTasks() { ... }
function loadTasks() { ... }
function saveFilter() { ... }
function loadFilter() { ... }

// 4. Task Management Functions
function addTask(taskText) { ... }
function deleteTask(taskId) { ... }
function toggleTaskComplete(taskId) { ... }

// 5. Filter Functions
function setFilter(filterValue) { ... }
function getFilteredTasks() { ... }

// 6. Rendering Functions
function renderTasks() { ... }
function renderFilterButtons() { ... }

// 7. Event Handlers
function handleFormSubmit(event) { ... }
function handleCheckboxChange(taskId) { ... }
function handleDeleteClick(taskId) { ... }
function handleFilterClick(filterValue) { ... }

// 8. Initialization
function init() { ... }
document.addEventListener('DOMContentLoaded', init);
```

---

## Notes

- All functions use simple, descriptive names following constitutional simplicity principles
- Pure functions (utility, filter) have no side effects
- Functions with side effects clearly documented
- Each function has a single, clear responsibility
- Event handlers wrap core functions for separation of concerns
- All functions will have detailed comment blocks in implementation

---

## Related Documents

- [data-model.md](../data-model.md) - Data structures and validation
- [spec.md](../spec.md) - Feature requirements
- [quickstart.md](../quickstart.md) - Testing these functions
