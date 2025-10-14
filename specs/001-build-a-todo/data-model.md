# Data Model: Todo List Application

**Feature**: Todo List Application
**Date**: 2025-10-11
**Status**: Complete

## Overview

This document defines the data structures, storage format, and state management for the todo list application. The data model is intentionally simple to support beginner-friendliness and constitutional compliance.

---

## Entity: Task

### Description
Represents a single todo item with description, completion status, and unique identifier.

### Properties

| Property | Type | Required | Description | Validation |
|----------|------|----------|-------------|------------|
| `id` | Number | Yes | Unique identifier for the task | Positive integer, typically timestamp |
| `text` | String | Yes | User-entered task description | 1-1000 characters, trimmed |
| `completed` | Boolean | Yes | Whether task is marked complete | `true` or `false` |

### Example Object

```javascript
{
  id: 1697123456789,
  text: "Buy groceries",
  completed: false
}
```

### Validation Rules

1. **ID Generation**:
   - Generated using `Date.now()` for uniqueness
   - No manual setting allowed
   - Collision risk negligible for single-user, sequential creation

2. **Text Validation**:
   - Must not be empty string after trimming whitespace
   - Maximum length: 1000 characters
   - All characters allowed (including emojis, special characters)
   - HTML characters will be escaped during rendering to prevent XSS

3. **Completed Status**:
   - Only boolean values allowed
   - Defaults to `false` on creation
   - Toggleable via user action

### State Transitions

```
[New Task Created] → completed: false
      ↓
[User Clicks Checkbox] → completed: true
      ↓
[User Clicks Checkbox Again] → completed: false
      ↓
[User Deletes Task] → Removed from array
```

---

## Entity: TaskList (Collection)

### Description
In-memory array containing all Task objects. This is the single source of truth for application state.

### Structure

```javascript
// Global application state
let tasks = []; // Array of Task objects
```

### Operations

| Operation | Method | Description |
|-----------|--------|-------------|
| Create | `tasks.push(newTask)` | Add new task to end of array |
| Read | `tasks[index]` or `tasks.filter(...)` | Access task(s) by index or filter |
| Update | `tasks[index].completed = !tasks[index].completed` | Toggle task completion |
| Delete | `tasks.splice(index, 1)` | Remove task from array |
| Filter | `tasks.filter(t => !t.completed)` | Get subset of tasks |

### Example Array

```javascript
[
  { id: 1697123456789, text: "Buy groceries", completed: false },
  { id: 1697123457000, text: "Walk the dog", completed: true },
  { id: 1697123458000, text: "Finish homework", completed: false }
]
```

---

## Entity: FilterState

### Description
Represents the currently active view filter. Determines which subset of tasks to display.

### Properties

| Property | Type | Description | Possible Values |
|----------|------|-------------|-----------------|
| `currentFilter` | String | Active filter selection | `"all"`, `"active"`, `"completed"` |

### Storage

```javascript
// Global filter state
let currentFilter = 'all'; // Default: show all tasks
```

### Filter Behavior

| Filter Value | Display Logic | Tasks Shown |
|--------------|---------------|-------------|
| `"all"` | No filtering | All tasks regardless of completion status |
| `"active"` | `task.completed === false` | Only incomplete tasks |
| `"completed"` | `task.completed === true` | Only completed tasks |

### Example Usage

```javascript
// Get filtered tasks for rendering
function getFilteredTasks() {
  if (currentFilter === 'active') {
    return tasks.filter(task => !task.completed);
  } else if (currentFilter === 'completed') {
    return tasks.filter(task => task.completed);
  }
  return tasks; // 'all' filter
}
```

---

## Storage Layer: localStorage

### Description
Browser's localStorage API persists tasks and filter state between sessions.

### Storage Keys

| Key | Value Type | Content | Example |
|-----|------------|---------|---------|
| `todoAppTasks` | JSON String | Serialized array of Task objects | `"[{\"id\":123,\"text\":\"Buy milk\",\"completed\":false}]"` |
| `todoAppFilter` | String | Current filter selection | `"active"` |

### Serialization Format

**Tasks Storage**:
```javascript
// Save tasks
localStorage.setItem('todoAppTasks', JSON.stringify(tasks));

// Load tasks
const storedTasks = localStorage.getItem('todoAppTasks');
tasks = storedTasks ? JSON.parse(storedTasks) : [];
```

**Filter Storage**:
```javascript
// Save filter
localStorage.setItem('todoAppFilter', currentFilter);

// Load filter
currentFilter = localStorage.getItem('todoAppFilter') || 'all';
```

### Data Flow Diagram

```
User Action (Add/Toggle/Delete)
    ↓
Update in-memory tasks array
    ↓
Save to localStorage (JSON.stringify)
    ↓
Re-render UI
```

```
Page Load
    ↓
Read from localStorage
    ↓
Parse JSON to tasks array
    ↓
Render initial UI
```

---

## Application State

### Global Variables

The application maintains minimal global state:

```javascript
// Core data
let tasks = [];           // Array of Task objects

// UI state
let currentFilter = 'all'; // Current filter: 'all', 'active', or 'completed'
```

### State Management Rules

1. **Single Source of Truth**: The `tasks` array is the only source for task data
2. **Unidirectional Flow**: Actions → Update State → Save → Re-render
3. **Immutability (Optional)**: For simplicity, we mutate the array directly, but could use immutable patterns for advanced learners
4. **No Hidden State**: All application state is in explicit variables

---

## Data Validation

### Input Validation (Task Creation)

```javascript
function isValidTaskText(text) {
  // Check if text is non-empty after trimming whitespace
  const trimmedText = text.trim();

  if (trimmedText.length === 0) {
    return false; // Empty or only whitespace
  }

  if (trimmedText.length > 1000) {
    return false; // Exceeds maximum length
  }

  return true;
}
```

### Storage Validation (Load from localStorage)

```javascript
function loadTasks() {
  try {
    const storedTasks = localStorage.getItem('todoAppTasks');

    if (!storedTasks) {
      return []; // No stored data, start fresh
    }

    const parsed = JSON.parse(storedTasks);

    // Validate it's an array
    if (!Array.isArray(parsed)) {
      console.warn('Invalid tasks data, starting fresh');
      return [];
    }

    // Validate each task object has required properties
    return parsed.filter(task =>
      task.id &&
      typeof task.text === 'string' &&
      typeof task.completed === 'boolean'
    );

  } catch (error) {
    console.error('Error loading tasks:', error);
    return []; // On error, start with empty array
  }
}
```

---

## Error Handling

### localStorage Quota Exceeded

```javascript
function saveTasks() {
  try {
    localStorage.setItem('todoAppTasks', JSON.stringify(tasks));
  } catch (error) {
    if (error.name === 'QuotaExceededError') {
      alert('Storage limit reached. Please delete some tasks to continue.');
      // Optionally: revert the last action that caused overflow
    } else {
      console.error('Error saving tasks:', error);
    }
  }
}
```

### localStorage Unavailable

```javascript
function isLocalStorageAvailable() {
  try {
    const testKey = '__localStorage_test__';
    localStorage.setItem(testKey, 'test');
    localStorage.removeItem(testKey);
    return true;
  } catch (error) {
    return false;
  }
}

// On page load
if (!isLocalStorageAvailable()) {
  alert('Warning: Data will not be saved. Your browser may have disabled storage.');
  // Continue with in-memory mode
}
```

---

## Performance Considerations

### Array Operations

For 1000+ tasks, these operations remain performant:

| Operation | Complexity | Performance |
|-----------|------------|-------------|
| Add task (push) | O(1) | Instant |
| Delete task (splice) | O(n) | Fast for reasonable n |
| Toggle complete | O(1) | Instant |
| Filter tasks | O(n) | Fast for n < 10,000 |
| Render all tasks | O(n) | Fast for n < 1,000 |

### Optimization Notes

- No optimization needed for MVP scope (up to 1000 tasks)
- If scaling beyond 1000 tasks, consider:
  - Virtual scrolling for rendering
  - Pagination or lazy loading
  - IndexedDB for larger storage
  - Web Workers for heavy filtering

---

## Schema Version

**Version**: 1.0.0

### Future Migration Considerations

If data structure changes in future versions:

1. Add version field to stored data:
   ```javascript
   {
     version: "1.0.0",
     tasks: [...]
   }
   ```

2. Implement migration functions:
   ```javascript
   function migrateData(storedData) {
     if (storedData.version === "1.0.0") {
       // Already current version
       return storedData.tasks;
     }
     // Handle older versions
   }
   ```

Currently not needed for MVP, but documented for future reference.

---

## Data Flow Summary

### Task Creation Flow
```
User types text → Click "Add" button
  → Validate text (not empty, < 1000 chars)
  → Create Task object {id, text, completed: false}
  → Push to tasks array
  → Save to localStorage
  → Clear input field
  → Re-render task list
  → Announce to screen reader
```

### Task Toggle Flow
```
User clicks checkbox
  → Find task by id
  → Toggle completed property
  → Save to localStorage
  → Re-render task list
  → Update visual state (strikethrough/normal)
```

### Task Delete Flow
```
User clicks delete button
  → Find task index by id
  → Remove from array (splice)
  → Save to localStorage
  → Re-render task list
  → Announce removal to screen reader
```

### Filter Change Flow
```
User clicks filter button
  → Update currentFilter variable
  → Save filter to localStorage
  → Re-render task list (with filter applied)
  → Update filter button active states
```

### Page Load Flow
```
Page loads
  → Check localStorage availability
  → Load tasks from localStorage (or default to [])
  → Load filter from localStorage (or default to 'all')
  → Render initial task list
  → Setup event listeners
```

---

## Related Documents

- [spec.md](./spec.md) - Feature requirements and user stories
- [research.md](./research.md) - Technology decisions and rationale
- [quickstart.md](./quickstart.md) - Setup and testing guide
- [plan.md](./plan.md) - Overall implementation plan

---

## Notes

- This data model prioritizes simplicity and beginner comprehension over advanced patterns
- All structures are plain JavaScript objects and arrays (no classes needed)
- Direct array mutation is used for simplicity (immutability patterns available for advanced learners)
- localStorage provides sufficient persistence for single-user, client-side application
- Model supports all functional requirements from spec.md without unnecessary complexity
