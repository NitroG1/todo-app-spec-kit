# Function Interfaces: Add Due Dates to Todos

**Feature**: 002-add-due-dates
**Date**: 2025-10-14
**Purpose**: Define function signatures and interfaces for due date functionality

This document specifies the function interfaces for implementing due date support in the todo list application. All functions use simple, beginner-friendly patterns consistent with constitutional principles.

## Core Due Date Functions

### setTodoDueDate()

Sets or updates the due date for a specific todo.

```javascript
/**
 * Set or update the due date for a todo
 * @param {number} todoId - The ID of the todo to update
 * @param {string} dueDateString - ISO 8601 date string (YYYY-MM-DD)
 * @returns {boolean} - True if successful, false if todo not found or invalid date
 */
function setTodoDueDate(todoId, dueDateString) {
  // Validate date format
  // Find todo by ID in todos array
  // Update todo.dueDate
  // Save to localStorage
  // Re-render todo list
  // Announce change to screen readers
  // Return success status
}
```

**Usage Example**:
```javascript
setTodoDueDate(1697289600000, "2025-10-20"); // Set due date to Oct 20, 2025
```

---

### removeTodoDueDate()

Removes the due date from a specific todo (sets to null).

```javascript
/**
 * Remove the due date from a todo
 * @param {number} todoId - The ID of the todo to update
 * @returns {boolean} - True if successful, false if todo not found
 */
function removeTodoDueDate(todoId) {
  // Find todo by ID in todos array
  // Set todo.dueDate = null
  // Save to localStorage
  // Re-render todo list
  // Announce change to screen readers
  // Return success status
}
```

**Usage Example**:
```javascript
removeTodoDueDate(1697289600000); // Clear due date from todo
```

---

### getDueDateStatus()

Calculates the status of a due date relative to today.

```javascript
/**
 * Get the status of a due date relative to today
 * @param {string|null} dueDateString - ISO 8601 date string (YYYY-MM-DD) or null
 * @returns {string} - Status: 'none', 'overdue', 'today', or 'future'
 */
function getDueDateStatus(dueDateString) {
  // If null, return 'none'
  // Get today's date (normalized to midnight)
  // Parse dueDateString to Date object
  // Compare dates:
  //   - dueDate < today => 'overdue'
  //   - dueDate === today => 'today'
  //   - dueDate > today => 'future'
  // Return status string
}
```

**Usage Example**:
```javascript
getDueDateStatus("2025-10-10"); // "overdue" (if today is after Oct 10)
getDueDateStatus("2025-10-14"); // "today" (if today is Oct 14)
getDueDateStatus("2025-10-20"); // "future" (if today is before Oct 20)
getDueDateStatus(null);         // "none"
```

---

## Display and Formatting Functions

### formatDueDateForDisplay()

Formats an ISO 8601 date string for user-friendly display.

```javascript
/**
 * Format a due date for user-friendly display
 * @param {string|null} dueDateString - ISO 8601 date string (YYYY-MM-DD) or null
 * @returns {string} - Formatted date (e.g., "Oct 14, 2025") or empty string if null
 */
function formatDueDateForDisplay(dueDateString) {
  // If null, return empty string
  // Parse date string to Date object
  // Format using toLocaleDateString() or custom formatting
  // Return formatted string
}
```

**Usage Example**:
```javascript
formatDueDateForDisplay("2025-10-14"); // "Oct 14, 2025"
formatDueDateForDisplay(null);         // ""
```

---

### getDueDateIconAndLabel()

Gets the appropriate icon and ARIA label for a due date status.

```javascript
/**
 * Get icon and ARIA label for a due date status
 * @param {string} status - Status from getDueDateStatus() ('none', 'overdue', 'today', 'future')
 * @returns {object} - {icon: string, label: string, cssClass: string}
 */
function getDueDateIconAndLabel(status) {
  // Map status to appropriate icon, label, and CSS class:
  //   'overdue' => {icon: '⚠️', label: 'Warning: Task is overdue', cssClass: 'overdue'}
  //   'today'   => {icon: '📅', label: 'Due today', cssClass: 'due-today'}
  //   'future'  => {icon: '', label: '', cssClass: ''}
  //   'none'    => {icon: '', label: '', cssClass: ''}
  // Return object
}
```

**Usage Example**:
```javascript
getDueDateIconAndLabel('overdue'); // {icon: '⚠️', label: 'Warning: Task is overdue', cssClass: 'overdue'}
getDueDateIconAndLabel('today');   // {icon: '📅', label: 'Due today', cssClass: 'due-today'}
```

---

## Sorting Functions

### sortTodosByDueDate()

Sorts the todos array by due date.

```javascript
/**
 * Sort todos by due date
 * @param {Array} todos - Array of todo objects
 * @param {string} direction - Sort direction: 'asc' (ascending) or 'desc' (descending)
 * @returns {Array} - Sorted array (new array, does not mutate original)
 */
function sortTodosByDueDate(todos, direction = 'asc') {
  // Create a copy of todos array
  // Sort using compareDueDates function
  //   - Todos with null dueDate go to the end
  //   - Compare ISO 8601 strings lexicographically
  //   - Reverse if direction === 'desc'
  // Return sorted array
}
```

**Usage Example**:
```javascript
const sorted = sortTodosByDueDate(todos, 'asc');  // Earliest first, nulls last
const sorted = sortTodosByDueDate(todos, 'desc'); // Latest first, nulls last
```

---

### compareDueDates()

Comparison function for sorting todos by due date.

```javascript
/**
 * Compare function for sorting todos by due date (ascending)
 * @param {object} todoA - First todo object
 * @param {object} todoB - Second todo object
 * @returns {number} - Negative if A < B, positive if A > B, 0 if equal
 */
function compareDueDates(todoA, todoB) {
  // If both have no due date, return 0 (maintain order)
  // If only A has no due date, return 1 (A goes after B)
  // If only B has no due date, return -1 (A goes before B)
  // Compare ISO 8601 strings: todoA.dueDate.localeCompare(todoB.dueDate)
  // Return comparison result
}
```

**Usage Example**:
```javascript
todos.sort(compareDueDates); // Sort ascending (earliest first, nulls last)
```

---

## Filtering Functions

### filterTodosByDueDateCriteria()

Filters todos based on due date criteria.

```javascript
/**
 * Filter todos by due date criteria
 * @param {Array} todos - Array of todo objects
 * @param {string} criteria - Filter criteria: 'all', 'overdue', 'today', 'week', 'none'
 * @returns {Array} - Filtered array (new array)
 */
function filterTodosByDueDateCriteria(todos, criteria) {
  // If criteria === 'all', return copy of todos array
  // If criteria === 'overdue', filter by getDueDateStatus() === 'overdue'
  // If criteria === 'today', filter by getDueDateStatus() === 'today'
  // If criteria === 'week', filter by isWithinNextWeek()
  // If criteria === 'none', filter by dueDate === null
  // Return filtered array
}
```

**Usage Example**:
```javascript
const overdue = filterTodosByDueDateCriteria(todos, 'overdue'); // Only overdue tasks
const today = filterTodosByDueDateCriteria(todos, 'today');     // Only tasks due today
```

---

### isWithinNextWeek()

Checks if a due date falls within the next 7 days.

```javascript
/**
 * Check if a due date is within the next 7 days (including today)
 * @param {string|null} dueDateString - ISO 8601 date string (YYYY-MM-DD) or null
 * @returns {boolean} - True if within next 7 days, false otherwise
 */
function isWithinNextWeek(dueDateString) {
  // If null, return false
  // Get today (normalized to midnight)
  // Calculate date 7 days from today
  // Parse dueDateString
  // Check if dueDate >= today AND dueDate <= (today + 7 days)
  // Return boolean result
}
```

**Usage Example**:
```javascript
isWithinNextWeek("2025-10-18"); // true (if today is Oct 14 and Oct 18 is within 7 days)
isWithinNextWeek("2025-11-01"); // false (more than 7 days away)
isWithinNextWeek(null);         // false
```

---

## UI Event Handlers

### handleEditIconClick()

Event handler for edit icon button clicks.

```javascript
/**
 * Handle click on edit icon to show/hide inline date picker
 * @param {Event} event - Click event
 * @returns {void}
 */
function handleEditIconClick(event) {
  // Get the todo ID from the button's data attribute or parent element
  // Toggle visibility of the date picker for this todo
  // If showing picker, focus the date input element
  // If hiding picker, return focus to edit button
}
```

**Usage Example**:
```javascript
editIconButton.addEventListener('click', handleEditIconClick);
```

---

### handleDueDateChange()

Event handler for date picker value changes.

```javascript
/**
 * Handle change in date picker value
 * @param {Event} event - Change event from date input
 * @param {number} todoId - ID of the todo being edited
 * @returns {void}
 */
function handleDueDateChange(event, todoId) {
  // Get the new date value from event.target.value
  // If value is empty, call removeTodoDueDate(todoId)
  // Otherwise, call setTodoDueDate(todoId, newDate)
  // Hide the date picker
  // Return focus to edit icon button
}
```

**Usage Example**:
```javascript
dateInput.addEventListener('change', (e) => handleDueDateChange(e, todoId));
```

---

### handleSortChange()

Event handler for sort dropdown changes.

```javascript
/**
 * Handle change in sort selection
 * @param {Event} event - Change event from sort dropdown
 * @returns {void}
 */
function handleSortChange(event) {
  // Get selected sort option from event.target.value
  // If 'none', render todos in original order
  // If 'due-asc', sort by due date ascending and render
  // If 'due-desc', sort by due date descending and render
  // Store current sort preference in memory (not localStorage)
  // Announce sort change to screen readers
}
```

**Usage Example**:
```javascript
sortSelect.addEventListener('change', handleSortChange);
```

---

### handleFilterClick()

Event handler for filter button clicks.

```javascript
/**
 * Handle click on filter button
 * @param {Event} event - Click event from filter button
 * @returns {void}
 */
function handleFilterClick(event) {
  // Get filter criteria from button's data-filter attribute
  // Update active button styling (remove 'active' from others, add to clicked)
  // Filter todos using filterTodosByDueDateCriteria()
  // Re-render todo list with filtered results
  // Store current filter preference in memory (not localStorage)
  // Announce filter change to screen readers
}
```

**Usage Example**:
```javascript
filterButton.addEventListener('click', handleFilterClick);
```

---

## Data Migration and Validation

### migrateTasksSchema()

Migrates todos from schema v1 (no dueDate) to schema v2 (with dueDate).

```javascript
/**
 * Migrate todos to include dueDate field
 * Adds dueDate: null to any todo missing the field
 * This is a one-time, non-destructive migration
 * @returns {void}
 */
function migrateTasksSchema() {
  // Load todos from localStorage
  // Initialize updated flag to false
  // For each todo:
  //   If todo does not have 'dueDate' property:
  //     Set todo.dueDate = null
  //     Set updated flag to true
  // If updated flag is true:
  //   Save todos back to localStorage
  //   Log migration completion to console
}
```

**Usage Example**:
```javascript
// Call once during app initialization
document.addEventListener('DOMContentLoaded', () => {
  migrateTasksSchema();
  // ... continue with normal initialization
});
```

---

### isValidDueDate()

Validates a due date string.

```javascript
/**
 * Validate a due date string
 * @param {string|null} dueDateString - Date string to validate
 * @returns {boolean} - True if valid (or null), false otherwise
 */
function isValidDueDate(dueDateString) {
  // If null, return true (null is valid - no due date)
  // If not a string, return false
  // Check format matches /^\d{4}-\d{2}-\d{2}$/ (ISO 8601)
  // Try parsing with new Date() - check if Invalid Date
  // Check year is within range 2020-2099
  // Return validation result
}
```

**Usage Example**:
```javascript
isValidDueDate("2025-10-14"); // true
isValidDueDate("2025-13-45"); // false (invalid month/day)
isValidDueDate("not-a-date"); // false (invalid format)
isValidDueDate(null);         // true (null is valid)
```

---

## Accessibility Functions

### announceDueDateChange()

Announces due date changes to screen readers via ARIA live region.

```javascript
/**
 * Announce due date change to screen readers
 * @param {string} message - Message to announce (e.g., "Due date set to October 14, 2025")
 * @returns {void}
 */
function announceDueDateChange(message) {
  // Get ARIA live region element
  // Set textContent to message
  // Clear after 1 second (so it can be reused for next announcement)
}
```

**Usage Example**:
```javascript
announceDueDateChange("Due date set to October 14, 2025");
announceDueDateChange("Due date removed");
```

---

## Summary

This document defines 17 key function interfaces for due date functionality:

**Core Operations** (3):
- `setTodoDueDate()` - Set/update due date
- `removeTodoDueDate()` - Remove due date
- `getDueDateStatus()` - Calculate date status

**Display** (2):
- `formatDueDateForDisplay()` - Format date for UI
- `getDueDateIconAndLabel()` - Get status icon/label

**Sorting** (2):
- `sortTodosByDueDate()` - Sort todos by date
- `compareDueDates()` - Date comparison function

**Filtering** (2):
- `filterTodosByDueDateCriteria()` - Filter by criteria
- `isWithinNextWeek()` - Check 7-day window

**UI Handlers** (4):
- `handleEditIconClick()` - Show/hide date picker
- `handleDueDateChange()` - Process date selection
- `handleSortChange()` - Handle sort dropdown
- `handleFilterClick()` - Handle filter buttons

**Migration & Validation** (2):
- `migrateTasksSchema()` - Upgrade data schema
- `isValidDueDate()` - Validate date format

**Accessibility** (1):
- `announceDueDateChange()` - Screen reader announcements

All functions use simple, descriptive names and follow beginner-friendly patterns per constitutional Principle I.
