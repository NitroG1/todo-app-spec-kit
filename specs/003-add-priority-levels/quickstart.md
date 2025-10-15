# Quickstart Guide: Priority Levels Implementation

**Feature**: Priority Levels for Todos
**Date**: 2025-10-14
**Audience**: Developers implementing this feature

## Overview

This guide provides a step-by-step walkthrough for implementing priority levels in the todo app. Follow these steps in order to ensure a smooth implementation that adheres to project principles.

---

## Prerequisites

- ✅ Reviewed [spec.md](spec.md) - Feature requirements and user stories
- ✅ Reviewed [research.md](research.md) - Design decisions and rationale
- ✅ Reviewed [data-model.md](data-model.md) - Schema changes and validation rules
- ✅ Reviewed [constitution.md](../../.specify/memory/constitution.md) - Project principles
- ✅ Familiar with existing codebase (index.html, especially due dates feature)

---

## Implementation Order

### Phase 1: Data Layer (Foundation)
1. Schema migration
2. Priority validation
3. Priority field in task creation

### Phase 2: UI Layer (Visual Indicators)
4. CSS styles for priority badges
5. Render priority badges in todo list
6. Priority selector in creation form

### Phase 3: Editing (User Interaction)
7. Inline priority editor
8. Update priority function

### Phase 4: Filtering (Advanced)
9. Priority filter buttons
10. Filter logic integration
11. Session persistence

---

## Step-by-Step Implementation

### Step 1: Schema Migration Function

**Location**: `<script>` section, in "Storage Functions" area (after `migrateTasksSchema`)

**What to add**:

```javascript
/**
 * Migrate todos from schema v2 to schema v3
 * Adds priority: 'medium' to any todo missing the field
 * This is a non-destructive, one-time operation
 *
 * @returns {void}
 */
function migratePrioritySchema() {
  // Load current tasks from localStorage
  const loadedTasks = loadTasks();

  // Track if any tasks were updated
  let updated = false;

  // Check each task for the priority field
  loadedTasks.forEach(task => {
    // If task is missing the priority field, add it with 'medium' value
    if (!task.hasOwnProperty('priority')) {
      task.priority = 'medium';
      updated = true;
    }
  });

  // Save back to localStorage only if changes were made
  if (updated) {
    // Update global tasks array
    tasks = loadedTasks;
    saveTasks();
    console.log(`Priority migration complete: Updated ${loadedTasks.length} todos`);
  }
}
```

**Testing**:
- Create todos with old schema (manually edit localStorage to remove priority field)
- Reload page, verify migration adds `priority: 'medium'`
- Check console for migration message

---

### Step 2: Priority Validation Function

**Location**: `<script>` section, in "Utility Functions" area (after `isValidDueDate`)

**What to add**:

```javascript
/**
 * Validate a priority value
 * Checks if the priority string is one of the three valid values
 *
 * @param {string} priority - Priority value to validate
 * @returns {boolean} True if valid, false otherwise
 */
function isValidPriority(priority) {
  // Must be a string
  if (typeof priority !== 'string') {
    return false;
  }

  // Normalize to lowercase for case-insensitive comparison
  const normalized = priority.toLowerCase();

  // Check if it's one of the three valid values
  return ['high', 'medium', 'low'].includes(normalized);
}

/**
 * Get the CSS class for a priority level
 * Maps priority values to their corresponding CSS class names
 *
 * @param {string} priority - Priority value ('high', 'medium', or 'low')
 * @returns {string} CSS class name
 */
function getPriorityClass(priority) {
  if (!isValidPriority(priority)) {
    return 'priority-medium'; // Default fallback
  }

  const normalized = priority.toLowerCase();
  return `priority-${normalized}`;
}

/**
 * Get the display label for a priority level
 * Returns abbreviated labels for space efficiency
 *
 * @param {string} priority - Priority value ('high', 'medium', or 'low')
 * @returns {string} Display label ('High', 'Med', or 'Low')
 */
function getPriorityLabel(priority) {
  if (!isValidPriority(priority)) {
    return 'Med'; // Default fallback
  }

  const normalized = priority.toLowerCase();

  if (normalized === 'high') return 'High';
  if (normalized === 'medium') return 'Med';
  if (normalized === 'low') return 'Low';

  return 'Med'; // Fallback
}
```

**Testing**:
- Test with valid values: `isValidPriority('high')` → true
- Test with invalid values: `isValidPriority('urgent')` → false
- Test case insensitivity: `isValidPriority('HIGH')` → true

---

### Step 3: Update Task Creation

**Location**: `addTask()` function in "Task Management Functions" section

**What to change**:

```javascript
// BEFORE:
const newTask = {
  id: generateTaskId(),
  text: taskText.trim(),
  completed: false,
  dueDate: null
};

// AFTER:
const newTask = {
  id: generateTaskId(),
  text: taskText.trim(),
  completed: false,
  dueDate: null,
  priority: getPriorityFromForm() // Get selected priority from form
};
```

**New helper function** (add in "Utility Functions"):

```javascript
/**
 * Get the selected priority from the creation form
 * Reads the priority dropdown value and validates it
 *
 * @returns {string} Valid priority value ('high', 'medium', or 'low')
 */
function getPriorityFromForm() {
  const prioritySelect = document.getElementById('priority-select');

  if (!prioritySelect) {
    console.warn('Priority selector not found, defaulting to medium');
    return 'medium';
  }

  const value = prioritySelect.value;

  if (!isValidPriority(value)) {
    console.warn('Invalid priority selected, defaulting to medium');
    return 'medium';
  }

  return value.toLowerCase();
}
```

---

### Step 4: CSS Styles for Priority Badges

**Location**: `<style>` section, add after "Due Date Display" section

**What to add**:

```css
/* === Priority Badges === */
/* Styles for priority level visual indicators (color + text) */

/* Priority badge container */
.priority-badge {
  display: inline-flex;
  align-items: center;
  padding: 0.25rem 0.5rem;
  font-size: 0.75rem;
  font-weight: 600;
  border-radius: 4px;
  margin-right: 0.5rem;
  cursor: pointer;
  transition: opacity 0.2s, transform 0.1s;
  border: 1px solid;
}

.priority-badge:hover {
  opacity: 0.8;
  transform: scale(1.05);
}

.priority-badge:focus {
  outline: 3px solid #3498db;
  outline-offset: 2px;
}

/* High priority - Red theme */
.priority-high {
  background-color: #fee;
  color: #800;
  border-color: #c00;
}

/* Medium priority - Orange theme */
.priority-medium {
  background-color: #fef3e0;
  color: #c70;
  border-color: #f90;
}

/* Low priority - Blue theme */
.priority-low {
  background-color: #e6f4ff;
  color: #004999;
  border-color: #0066cc;
}

/* Priority selector in creation form */
.priority-select {
  padding: 0.75rem 1rem;
  font-size: 1rem;
  border: 2px solid #ddd;
  border-radius: 4px;
  background-color: white;
  cursor: pointer;
  transition: border-color 0.2s;
  min-width: 120px;
}

.priority-select:focus {
  outline: none;
  border-color: #3498db;
}

/* Priority inline editor (similar to due date picker) */
.priority-editor {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-top: 0.5rem;
  padding: 0.5rem;
  background-color: #f9f9f9;
  border-radius: 4px;
  border: 1px solid #ddd;
}

.priority-editor select {
  padding: 0.5rem;
  font-size: 0.9rem;
  border: 2px solid #ddd;
  border-radius: 4px;
  cursor: pointer;
}

.priority-editor select:focus {
  outline: none;
  border-color: #3498db;
}

/* Priority filter buttons in toolbar */
.priority-filter-controls {
  display: flex;
  gap: 0.25rem;
  flex-wrap: wrap;
  margin-top: 0.5rem;
}

.filter-btn-priority {
  padding: 0.4rem 0.75rem;
  font-size: 0.85rem;
  background-color: #ecf0f1;
  color: #555;
  border: 2px solid transparent;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.filter-btn-priority:hover {
  background-color: #d5dbdb;
}

.filter-btn-priority.active {
  background-color: #3498db;
  color: white;
  border-color: #2980b9;
}

.filter-btn-priority:focus {
  outline: 3px solid #3498db;
  outline-offset: 2px;
}

/* Completed tasks with priority badges - muted appearance */
.task-item.completed .priority-badge {
  opacity: 0.5;
}
```

**Testing**:
- Preview colors in browser, verify contrast ratios with DevTools
- Test hover/focus states with keyboard navigation

---

### Step 5: Render Priority Badges in Todo List

**Location**: `renderTasks()` function, modify the task rendering logic

**What to change**:

Find this section in `renderTasks()`:

```javascript
// Create task text span
const textSpan = document.createElement('span');
textSpan.className = 'task-text';
textSpan.textContent = task.text;
```

**Add BEFORE the textSpan creation**:

```javascript
// Create priority badge
const priorityBadge = document.createElement('button');
priorityBadge.className = `priority-badge ${getPriorityClass(task.priority)}`;
priorityBadge.textContent = getPriorityLabel(task.priority);
priorityBadge.setAttribute('aria-label', `Priority: ${task.priority}. Click to edit.`);
priorityBadge.setAttribute('role', 'button');
priorityBadge.setAttribute('tabindex', '0');
priorityBadge.id = `priority-badge-${task.id}`;
priorityBadge.addEventListener('click', () => togglePriorityEditor(task.id));
```

**Then add priority badge to list item** (after checkbox, before textSpan):

```javascript
// Append all elements to list item
li.appendChild(checkbox);
li.appendChild(priorityBadge); // NEW: Add priority badge
li.appendChild(textSpan);
li.appendChild(editBtn); // Existing due date edit button
// ... rest of elements
```

**Testing**:
- Create todos with different priorities
- Verify badges display with correct colors and labels
- Test click interaction (should open editor - implement next)

---

### Step 6: Priority Selector in Creation Form

**Location**: HTML `<form class="task-form">` in `<body>`

**What to change**:

Find this form:

```html
<form class="task-form" id="taskForm">
  <label for="taskInput">New task</label>
  <input type="text" id="taskInput" class="task-input" ...>
  <button type="submit" class="btn btn-primary">Add</button>
</form>
```

**Change to**:

```html
<form class="task-form" id="taskForm">
  <label for="taskInput">New task</label>
  <input
    type="text"
    id="taskInput"
    class="task-input"
    placeholder="What needs to be done?"
    maxlength="1000"
    autocomplete="off"
  >
  <label for="priority-select" class="sr-only">Priority</label>
  <select id="priority-select" class="priority-select">
    <option value="high">High</option>
    <option value="medium" selected>Medium</option>
    <option value="low">Low</option>
  </select>
  <button type="submit" class="btn btn-primary">Add</button>
</form>
```

**CSS adjustment** (update `.task-form` style):

```css
.task-form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
  flex-wrap: wrap; /* Allow wrapping on small screens */
}
```

**Testing**:
- Select different priorities in dropdown
- Create todos, verify priority is saved correctly
- Test keyboard navigation (Tab to dropdown, Arrow keys to select)

---

### Step 7: Inline Priority Editor

**Location**: Add new function in "Priority Management Functions" section (create this section)

**What to add**:

```javascript
// === Priority Management Functions ===

/**
 * Set or update the priority for a todo
 * Validates priority, updates todo, saves, and re-renders
 *
 * @param {number} todoId - The ID of the todo to update
 * @param {string} priority - Priority value ('high', 'medium', or 'low')
 * @returns {boolean} True if successful, false if todo not found or invalid priority
 */
function setTodoPriority(todoId, priority) {
  // Validate priority
  if (!isValidPriority(priority)) {
    console.warn('Invalid priority value:', priority);
    return false;
  }

  // Find todo by ID
  const todo = tasks.find(t => t.id === todoId);
  if (!todo) {
    console.warn('Todo not found:', todoId);
    return false;
  }

  // Normalize and update priority
  todo.priority = priority.toLowerCase();

  // Save to localStorage
  saveTasks();

  // Re-render the task list
  renderTasks();

  // Announce change to screen readers
  announceToScreenReader(`Priority changed to ${priority}`);

  return true;
}

/**
 * Toggle visibility of the inline priority editor for a todo
 * Shows/hides the editor and manages focus for accessibility
 *
 * @param {number} todoId - The ID of the todo being edited
 * @returns {void}
 */
function togglePriorityEditor(todoId) {
  // Get the priority editor container for this todo
  const editorContainer = document.getElementById(`priority-editor-${todoId}`);
  if (!editorContainer) {
    // Create editor if it doesn't exist
    createPriorityEditor(todoId);
    return;
  }

  // Toggle visibility
  const isHidden = editorContainer.style.display === 'none' || editorContainer.style.display === '';

  if (isHidden) {
    // Show the editor
    editorContainer.style.display = 'flex';

    // Focus the select element for keyboard accessibility
    const select = editorContainer.querySelector('select');
    if (select) {
      select.focus();
    }
  } else {
    // Hide the editor
    editorContainer.style.display = 'none';

    // Return focus to the priority badge
    const badge = document.getElementById(`priority-badge-${todoId}`);
    if (badge) {
      badge.focus();
    }
  }
}

/**
 * Create inline priority editor for a todo
 * Dynamically builds the editor UI and inserts into DOM
 *
 * @param {number} todoId - The ID of the todo being edited
 * @returns {void}
 */
function createPriorityEditor(todoId) {
  // Find the todo
  const todo = tasks.find(t => t.id === todoId);
  if (!todo) {
    return;
  }

  // Find the list item for this todo
  const taskItem = document.querySelector(`#priority-badge-${todoId}`).closest('.task-item');
  if (!taskItem) {
    return;
  }

  // Create editor container
  const editorContainer = document.createElement('div');
  editorContainer.id = `priority-editor-${todoId}`;
  editorContainer.className = 'priority-editor';
  editorContainer.style.display = 'flex';

  // Create select dropdown
  const select = document.createElement('select');
  select.className = 'priority-select';
  select.value = todo.priority;

  // Add options
  const options = ['high', 'medium', 'low'];
  options.forEach(opt => {
    const option = document.createElement('option');
    option.value = opt;
    option.textContent = opt.charAt(0).toUpperCase() + opt.slice(1);
    if (opt === todo.priority) {
      option.selected = true;
    }
    select.appendChild(option);
  });

  // Handle selection change
  select.addEventListener('change', () => {
    setTodoPriority(todoId, select.value);
    togglePriorityEditor(todoId);
  });

  // Append select to container
  editorContainer.appendChild(select);

  // Insert after priority badge
  const badge = document.getElementById(`priority-badge-${todoId}`);
  if (badge) {
    badge.after(editorContainer);
  }
}
```

**Testing**:
- Click priority badge, verify editor opens
- Select different priority, verify update works
- Test keyboard navigation (Tab to badge, Enter to open, Arrow keys to select)

---

### Step 8: Priority Filter Buttons

**Location**: HTML toolbar section

**What to add**:

Find the toolbar:

```html
<div class="toolbar">
  <div class="sort-controls">...</div>
  <div class="filter-controls">...</div>
</div>
```

**Add below filter-controls**:

```html
<div class="priority-filter-controls">
  <button class="btn filter-btn-priority active" data-filter-priority="all">All</button>
  <button class="btn filter-btn-priority" data-filter-priority="high">High</button>
  <button class="btn filter-btn-priority" data-filter-priority="medium">Medium</button>
  <button class="btn filter-btn-priority" data-filter-priority="low">Low</button>
</div>
```

---

### Step 9: Priority Filter Logic

**Location**: Add state variable and update `getFilteredTasks()`

**Add state variable** (top of script, with other state variables):

```javascript
// Current priority filter: 'all', 'high', 'medium', 'low'
let currentPriorityFilter = 'all';
```

**Update `getFilteredTasks()`** (add priority filtering):

```javascript
function getFilteredTasks() {
  let filtered = tasks;

  // Apply completion filter (all/active/completed)
  if (currentFilter === 'active') {
    filtered = filtered.filter(task => !task.completed);
  } else if (currentFilter === 'completed') {
    filtered = filtered.filter(task => task.completed);
  }

  // Apply due date filter (all/overdue/today/week/none)
  filtered = filterTodosByDueDateCriteria(filtered, currentDueDateFilter);

  // Apply priority filter (all/high/medium/low) - NEW
  if (currentPriorityFilter !== 'all') {
    filtered = filtered.filter(task =>
      task.priority === currentPriorityFilter
    );
  }

  // Apply sorting
  if (currentSort === 'due-asc') {
    filtered = sortTodosByDueDate(filtered, 'asc');
  } else if (currentSort === 'due-desc') {
    filtered = sortTodosByDueDate(filtered, 'desc');
  }

  return filtered;
}
```

**Add event handler**:

```javascript
/**
 * Handle priority filter button click event
 * Updates filter state and re-renders with filtered results
 *
 * @param {Event} event - Click event from filter button
 * @returns {void}
 */
function handlePriorityFilterClick(event) {
  // Get filter criteria from button's data attribute
  const filterCriteria = event.target.getAttribute('data-filter-priority');

  // Update current priority filter state
  currentPriorityFilter = filterCriteria;

  // Save to sessionStorage (session-only persistence)
  try {
    sessionStorage.setItem('todoAppPriorityFilter', filterCriteria);
  } catch (error) {
    console.warn('Could not save priority filter preference:', error);
  }

  // Update active button styling
  const filterButtons = document.querySelectorAll('.filter-btn-priority');
  filterButtons.forEach(btn => {
    if (btn.getAttribute('data-filter-priority') === filterCriteria) {
      btn.classList.add('active');
    } else {
      btn.classList.remove('active');
    }
  });

  // Re-render with new filter
  renderTasks();

  // Announce filter change to screen readers
  let message = 'Filter changed to ';
  if (filterCriteria === 'all') {
    message += 'all priorities';
  } else {
    message += `${filterCriteria} priority only`;
  }
  announceToScreenReader(message);
}
```

---

### Step 10: Initialize Priority Filter

**Location**: `init()` function

**What to add**:

```javascript
function init() {
  // ... existing initialization code ...

  // Run priority migration (before loading tasks)
  migratePrioritySchema();

  // ... existing task/filter loading ...

  // Restore priority filter from sessionStorage
  try {
    const savedPriorityFilter = sessionStorage.getItem('todoAppPriorityFilter');
    if (savedPriorityFilter && ['all', 'high', 'medium', 'low'].includes(savedPriorityFilter)) {
      currentPriorityFilter = savedPriorityFilter;
    }
  } catch (error) {
    console.warn('Could not restore priority filter state:', error);
  }

  // ... existing rendering code ...

  // Set up priority filter button event listeners
  const priorityFilterButtons = document.querySelectorAll('.filter-btn-priority');
  priorityFilterButtons.forEach(button => {
    button.addEventListener('click', handlePriorityFilterClick);
  });
}
```

---

## Testing Checklist

### Manual Testing

- [ ] **Creation**: Create todos with High, Medium, Low priorities
- [ ] **Display**: Verify badges show correct colors and labels
- [ ] **Editing**: Click badge, change priority, verify update
- [ ] **Filtering**: Click filter buttons, verify correct todos shown
- [ ] **Migration**: Clear localStorage, create old-schema todos, reload, verify migration
- [ ] **Validation**: Try invalid priority in console, verify defaults to medium
- [ ] **Persistence**: Reload page, verify priority values persist
- [ ] **Filter persistence**: Apply filter, reload, verify filter resets to "All"

### Accessibility Testing

- [ ] **Keyboard navigation**: Tab through all priority controls
- [ ] **Screen reader**: Test with NVDA/JAWS, verify announcements
- [ ] **Focus management**: Verify focus returns to badge after editing
- [ ] **Color contrast**: Test with WebAIM contrast checker (all pass WCAG AA)
- [ ] **Zoom**: Test at 200% zoom, verify layout doesn't break

### Integration Testing

- [ ] **With completion filter**: Filter by Active + High priority
- [ ] **With due date filter**: Filter by Overdue + High priority
- [ ] **With sorting**: Sort by due date + filter by priority
- [ ] **With all filters**: Combine completion, due date, priority filters

---

## Common Pitfalls

### Pitfall 1: Forgetting Migration

**Problem**: Existing todos don't have priority field → errors on render

**Solution**: Always run `migratePrioritySchema()` in `init()` before loading tasks

### Pitfall 2: Case Sensitivity

**Problem**: Comparing 'High' !== 'high' → validation fails

**Solution**: Always normalize to lowercase (`priority.toLowerCase()`)

### Pitfall 3: Missing Validation

**Problem**: Invalid priority values saved to storage → broken UI

**Solution**: Validate priority before saving, default to 'medium'

### Pitfall 4: Broken Focus Management

**Problem**: After editing, focus lost → bad keyboard experience

**Solution**: Always return focus to badge after closing editor

---

## Next Steps

After completing implementation:

1. Run full testing checklist
2. Test on multiple browsers (Chrome, Firefox, Safari, Edge)
3. Review code against Constitution principles
4. Generate tasks.md with `/speckit.tasks` command
5. Execute tasks and implement feature
6. Create pull request with screenshots

---

## Resources

- **Spec**: [spec.md](spec.md)
- **Research**: [research.md](research.md)
- **Data Model**: [data-model.md](data-model.md)
- **Constitution**: [../../.specify/memory/constitution.md](../../.specify/memory/constitution.md)
- **Existing Feature**: Due dates (reference for patterns)

---

## Questions?

If you encounter issues not covered in this guide:

1. Check the research.md for design rationale
2. Review the due dates feature (similar patterns)
3. Consult the constitution for principles
4. Test with browser DevTools for debugging
