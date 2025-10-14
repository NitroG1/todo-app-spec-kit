# Data Model: Add Due Dates to Todos

**Feature**: 002-add-due-dates
**Date**: 2025-10-14
**Purpose**: Define data structures and storage format for due date functionality

This document defines the extended data structures, storage format, and state management for due dates in the todo list application. The data model maintains simplicity and backward compatibility with existing todos.

## Entity: Todo (Extended)

### Structure

The existing Todo entity from feature 001 is extended with one additional optional field:

```javascript
{
  id: number,           // Unique identifier (timestamp-based, e.g., Date.now())
  text: string,         // The todo item description
  completed: boolean,   // Whether the task is marked as done
  dueDate: string|null  // ISO 8601 date string (YYYY-MM-DD) or null if no due date
}
```

### Field Specifications

#### `id` (number, required)
- **Purpose**: Unique identifier for the todo
- **Format**: Positive integer (typically timestamp from `Date.now()`)
- **Constraints**: Must be unique within the todos array
- **Example**: `1697289600000`
- **From**: Existing field from feature 001

#### `text` (string, required)
- **Purpose**: The todo item description/content
- **Format**: Non-empty string
- **Constraints**: Minimum 1 character, maximum 500 characters (practical limit)
- **Example**: `"Buy groceries"`
- **From**: Existing field from feature 001

#### `completed` (boolean, required)
- **Purpose**: Tracks whether the todo is marked as done
- **Format**: Boolean (`true` or `false`)
- **Default**: `false` for new todos
- **Example**: `false`
- **From**: Existing field from feature 001

#### `dueDate` (string|null, optional) **[NEW]**
- **Purpose**: Stores the due date for the todo
- **Format**: ISO 8601 date string `"YYYY-MM-DD"` or `null`
- **Constraints**:
  - If string: Must be valid ISO 8601 date format
  - Date range: Practical range 2020-01-01 to 2099-12-31
  - Time component: None (date-only)
  - Timezone: Stored in UTC-agnostic format (compared as local dates)
- **Default**: `null` (no due date assigned)
- **Examples**:
  - `"2025-10-14"` (October 14, 2025)
  - `"2025-12-31"` (December 31, 2025)
  - `null` (no due date)
- **Validation Rules**:
  - Must match regex: `/^\d{4}-\d{2}-\d{2}$/` OR be `null`
  - Must be parseable by `new Date(dueDate)` without returning Invalid Date
- **Storage**: Stored directly in localStorage as part of the todos array

### Example Data

#### Todo without due date (backward compatible with feature 001)
```javascript
{
  id: 1697289600000,
  text: "Review pull requests",
  completed: false,
  dueDate: null
}
```

#### Todo with due date (new functionality)
```javascript
{
  id: 1697289600001,
  text: "Submit quarterly report",
  completed: false,
  dueDate: "2025-10-18"
}
```

#### Completed todo with due date
```javascript
{
  id: 1697289600002,
  text: "Pay utility bills",
  completed: true,
  dueDate: "2025-10-12"
}
```

## Storage Format

### localStorage Structure

Todos are stored in localStorage under the key `"todos"` as a JSON-stringified array:

```javascript
// localStorage key: "todos"
// localStorage value (JSON string):
[
  {"id":1697289600000,"text":"Review pull requests","completed":false,"dueDate":null},
  {"id":1697289600001,"text":"Submit quarterly report","completed":false,"dueDate":"2025-10-18"},
  {"id":1697289600002,"text":"Pay utility bills","completed":true,"dueDate":"2025-10-12"}
]
```

### Storage Operations

#### Save to localStorage
```javascript
function saveTasksToStorage(tasks) {
  // Serialize todos array to JSON string
  const json = JSON.stringify(tasks);
  // Save to localStorage
  localStorage.setItem('todos', json);
}
```

#### Load from localStorage
```javascript
function loadTasksFromStorage() {
  // Retrieve JSON string from localStorage
  const json = localStorage.getItem('todos');
  // Parse JSON or return empty array if no data
  return json ? JSON.parse(json) : [];
}
```

### Storage Size Considerations

- **Per todo overhead with due date**: ~85 bytes average
  - Example: `{"id":1697289600000,"text":"Buy groceries","completed":false,"dueDate":"2025-10-14"}`
  - Overhead breakdown: Structure (20 bytes) + text (variable) + dueDate (10 bytes)
- **Due date field overhead**: +10 bytes (e.g., `"2025-10-14"`) or +4 bytes (`null`)
- **localStorage limit**: Typically 5-10MB per origin
- **Capacity**: Can store 50,000+ todos with due dates (well beyond practical use)

## Data Migration

### Schema Upgrade Strategy

When feature 002 is deployed, existing todos from feature 001 will not have the `dueDate` field. The migration strategy ensures backward compatibility:

```javascript
/**
 * Migrate todos from schema v1 (feature 001) to schema v2 (feature 002)
 * Adds dueDate: null to any todo missing the field
 * This is a non-destructive, one-time operation
 */
function migrateTasksSchema() {
  const tasks = loadTasksFromStorage();
  let updated = false;

  tasks.forEach(task => {
    // Check if todo is missing the dueDate field
    if (!task.hasOwnProperty('dueDate')) {
      // Add dueDate field with null value (no due date assigned)
      task.dueDate = null;
      updated = true;
    }
  });

  // Save back to localStorage only if changes were made
  if (updated) {
    saveTasksToStorage(tasks);
    console.log(`Migrated ${tasks.length} todos to include dueDate field`);
  }
}
```

### Migration Behavior

- **Timing**: Runs once on first page load after feature 002 deployment
- **Trigger**: Called during app initialization (before rendering todos)
- **Non-destructive**: Only adds missing field, never modifies existing data
- **Idempotent**: Safe to run multiple times (no effect after first run)
- **User-transparent**: No UI or user action required

## Derived Data: Due Date Status

While not stored, the "status" of a due date is derived at render time:

### Status Calculation
```javascript
/**
 * Calculate the status of a todo's due date
 * @param {string|null} dueDateString - ISO 8601 date string or null
 * @returns {string} - 'none', 'overdue', 'today', 'future'
 */
function getDueDateStatus(dueDateString) {
  if (!dueDateString) return 'none';

  const today = new Date();
  today.setHours(0, 0, 0, 0); // Normalize to midnight

  const dueDate = new Date(dueDateString + 'T00:00:00');

  if (dueDate < today) return 'overdue';
  if (dueDate.toDateString() === today.toDateString()) return 'today';
  return 'future';
}
```

### Status Values
- **`none`**: Todo has no due date (`dueDate === null`)
- **`overdue`**: Due date is in the past (`dueDate < today`)
- **`today`**: Due date is today (`dueDate === today`)
- **`future`**: Due date is in the future (`dueDate > today`)

## Validation Rules

### Due Date Validation

```javascript
/**
 * Validate a due date string
 * @param {string|null} dueDateString - Date string to validate
 * @returns {boolean} - True if valid or null, false otherwise
 */
function isValidDueDate(dueDateString) {
  // Null is valid (no due date)
  if (dueDateString === null) return true;

  // Must be a string
  if (typeof dueDateString !== 'string') return false;

  // Must match ISO 8601 date format (YYYY-MM-DD)
  const isoRegex = /^\d{4}-\d{2}-\d{2}$/;
  if (!isoRegex.test(dueDateString)) return false;

  // Must be a valid date (e.g., not "2025-13-45")
  const date = new Date(dueDateString);
  if (isNaN(date.getTime())) return false;

  // Date must be within practical range (2020-2099)
  const year = date.getFullYear();
  if (year < 2020 || year > 2099) return false;

  return true;
}
```

### Todo Validation

```javascript
/**
 * Validate a complete todo object
 * @param {object} todo - Todo object to validate
 * @returns {boolean} - True if valid, false otherwise
 */
function isValidTodo(todo) {
  // Required fields
  if (typeof todo.id !== 'number' || todo.id <= 0) return false;
  if (typeof todo.text !== 'string' || todo.text.trim().length === 0) return false;
  if (typeof todo.completed !== 'boolean') return false;

  // Optional dueDate field
  if (!isValidDueDate(todo.dueDate)) return false;

  return true;
}
```

## Sorting and Filtering

### Sort Order

When sorting by due date:

```javascript
/**
 * Compare function for sorting todos by due date
 * Sorts in ascending order (earliest dates first)
 * Todos without due dates appear at the end
 */
function compareDueDates(a, b) {
  // If both have no due date, maintain original order
  if (!a.dueDate && !b.dueDate) return 0;

  // Todos without due dates go to the end
  if (!a.dueDate) return 1;
  if (!b.dueDate) return -1;

  // Compare ISO 8601 strings (lexicographic comparison works for ISO dates)
  return a.dueDate.localeCompare(b.dueDate);
}

// Usage: tasks.sort(compareDueDates)
```

### Filter Predicates

```javascript
// Filter: Overdue todos only
function isOverdue(todo) {
  return getDueDateStatus(todo.dueDate) === 'overdue';
}

// Filter: Due today only
function isDueToday(todo) {
  return getDueDateStatus(todo.dueDate) === 'today';
}

// Filter: Due within next 7 days (including today)
function isDueThisWeek(todo) {
  if (!todo.dueDate) return false;

  const today = new Date();
  today.setHours(0, 0, 0, 0);

  const nextWeek = new Date(today);
  nextWeek.setDate(today.getDate() + 7);

  const dueDate = new Date(todo.dueDate + 'T00:00:00');

  return dueDate >= today && dueDate <= nextWeek;
}

// Filter: No due date
function hasNoDueDate(todo) {
  return todo.dueDate === null;
}
```

## Summary

- **Extended Entity**: `Todo` object gains optional `dueDate` field (ISO 8601 string or null)
- **Storage Format**: JSON array in localStorage under key `"todos"`
- **Migration**: Automatic addition of `dueDate: null` to existing todos on first load
- **Validation**: ISO 8601 format check, date range 2020-2099, null allowed
- **Derived Status**: `none`, `overdue`, `today`, `future` calculated at render time
- **Sorting**: Lexicographic comparison of ISO 8601 strings, nulls last
- **Storage Overhead**: +10 bytes per todo with date, negligible impact on capacity

This data model maintains backward compatibility with feature 001 while cleanly extending functionality for due dates.
