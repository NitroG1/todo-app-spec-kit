# Data Model: Priority Levels

**Feature**: Priority Levels for Todos
**Date**: 2025-10-14
**Status**: Complete

## Overview

This document defines the data structures and schema changes for the priority levels feature. The feature extends the existing Todo entity with a priority field and adds filter state management.

## Entity: Todo (Extended)

**Purpose**: Represents a single task with completion status, optional due date, and priority level

### Schema

```javascript
{
  id: number,           // Unique identifier (timestamp-based)
  text: string,         // Task description (1-1000 characters)
  completed: boolean,   // Completion status
  dueDate: string|null, // ISO 8601 date (YYYY-MM-DD) or null [existing]
  priority: string      // Priority level: 'high', 'medium', or 'low' [NEW]
}
```

### Field Specifications

| Field | Type | Required | Default | Validation | Notes |
|-------|------|----------|---------|------------|-------|
| `id` | `number` | Yes | `Date.now()` | Must be unique | Unchanged from existing schema |
| `text` | `string` | Yes | N/A | 1-1000 chars, non-empty after trim | Unchanged from existing schema |
| `completed` | `boolean` | Yes | `false` | Must be boolean | Unchanged from existing schema |
| `dueDate` | `string` or `null` | No | `null` | ISO 8601 format or null | Unchanged from existing schema |
| `priority` | `string` | Yes | `'medium'` | Must be `'high'`, `'medium'`, or `'low'` | **NEW FIELD** |

### Validation Rules

**Priority Field**:
- **Valid values**: Exactly one of `'high'`, `'medium'`, `'low'` (lowercase)
- **Case handling**: Normalize to lowercase if mixed case provided
- **Invalid value handling**: Reject and default to `'medium'` with console warning
- **Missing value**: Treat as `'medium'` during migration

**Example Validation Function**:

```javascript
/**
 * Validate a priority value
 * @param {string} priority - Priority value to validate
 * @returns {boolean} True if valid, false otherwise
 */
function isValidPriority(priority) {
  if (typeof priority !== 'string') {
    return false;
  }

  const normalized = priority.toLowerCase();
  return ['high', 'medium', 'low'].includes(normalized);
}
```

### State Transitions

**Priority Changes**:
- A todo's priority can change at any time (independent of completion status)
- Priority persists when todo is marked complete/incomplete
- Priority is preserved during due date edits
- Priority is included in localStorage serialization

**Migration from Schema v2 to v3**:
- Schema v2: Todo without `priority` field (features 001-002)
- Schema v3: Todo with `priority` field (feature 003+)
- Migration: Add `priority: 'medium'` to todos missing the field
- Non-destructive: Existing fields (`id`, `text`, `completed`, `dueDate`) unchanged

---

## Entity: FilterState (Extended)

**Purpose**: Tracks current UI filter selections (completion status, due date, priority)

### Schema

```javascript
{
  currentFilter: string,           // Completion filter: 'all', 'active', 'completed' [existing]
  currentSort: string,             // Sort order: 'none', 'due-asc', 'due-desc' [existing]
  currentDueDateFilter: string,    // Due date filter: 'all', 'overdue', 'today', 'week', 'none' [existing]
  currentPriorityFilter: string    // Priority filter: 'all', 'high', 'medium', 'low' [NEW]
}
```

### Field Specifications

| Field | Type | Default | Persistence | Valid Values |
|-------|------|---------|-------------|--------------|
| `currentFilter` | `string` | `'all'` | localStorage | `'all'`, `'active'`, `'completed'` |
| `currentSort` | `string` | `'none'` | sessionStorage | `'none'`, `'due-asc'`, `'due-desc'` |
| `currentDueDateFilter` | `string` | `'all'` | sessionStorage | `'all'`, `'overdue'`, `'today'`, `'week'`, `'none'` |
| `currentPriorityFilter` | `string` | `'all'` | sessionStorage | `'all'`, `'high'`, `'medium'`, `'low'` [**NEW**] |

### Validation Rules

**Priority Filter**:
- **Valid values**: Exactly one of `'all'`, `'high'`, `'medium'`, `'low'` (lowercase)
- **Invalid value handling**: Reset to `'all'` with console warning
- **Persistence**: Session-only (cleared on new browser session)

---

## Storage Structure

### localStorage

**Key**: `todoAppTasks`

**Value**: JSON array of Todo objects

```json
[
  {
    "id": 1728936000000,
    "text": "Buy groceries",
    "completed": false,
    "dueDate": "2025-10-15",
    "priority": "high"
  },
  {
    "id": 1728936001000,
    "text": "Call dentist",
    "completed": false,
    "dueDate": null,
    "priority": "medium"
  },
  {
    "id": 1728936002000,
    "text": "Read book chapter",
    "completed": true,
    "dueDate": "2025-10-14",
    "priority": "low"
  }
]
```

### sessionStorage

**Priority Filter Key**: `todoAppPriorityFilter`

**Value**: String (`'all'`, `'high'`, `'medium'`, `'low'`)

```json
"high"
```

---

## Migration Strategy

### Schema Version History

- **v1** (Feature 001): `{id, text, completed}`
- **v2** (Feature 002): `{id, text, completed, dueDate}`
- **v3** (Feature 003): `{id, text, completed, dueDate, priority}` ← Current

### Migration Function

**Name**: `migratePrioritySchema()`

**Trigger**: Run once on app initialization (before loading tasks into memory)

**Logic**:

```javascript
/**
 * Migrate todos from schema v2 to schema v3
 * Adds priority: 'medium' to any todo missing the field
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
    tasks = loadedTasks;
    saveTasks();
    console.log(`Migration complete: Added priority field to ${loadedTasks.length} todos`);
  }
}
```

**Migration Rules**:
1. Non-destructive: Never remove or modify existing fields
2. Idempotent: Safe to run multiple times (checks for field existence)
3. Default value: `'medium'` (neutral, non-disruptive)
4. Logging: Console message confirms migration completed

---

## Relationships

### Todo → Priority Display

**Mapping**: Todo.priority → Visual Representation

| Priority Value | Color | Label | CSS Class |
|----------------|-------|-------|-----------|
| `'high'` | Red (#fee bg, #c00 border) | "High" | `.priority-high` |
| `'medium'` | Orange (#fef3e0 bg, #f90 border) | "Med" | `.priority-medium` |
| `'low'` | Blue (#e6f4ff bg, #0066cc border) | "Low" | `.priority-low` |

### FilterState → Filtered Todos

**Logic**: Todos are filtered using AND logic across all active filters

```javascript
// Pseudo-code
filteredTodos = allTodos
  .filter(byCompletionStatus)  // currentFilter
  .filter(byDueDateCriteria)   // currentDueDateFilter
  .filter(byPriority)          // currentPriorityFilter (NEW)
  .sort(bySortCriteria)        // currentSort
```

**Priority Filter Logic**:
- `'all'`: Include all todos (no filtering)
- `'high'`: Include only `todo.priority === 'high'`
- `'medium'`: Include only `todo.priority === 'medium'`
- `'low'`: Include only `todo.priority === 'low'`

---

## Data Integrity

### Validation on Load

When loading todos from localStorage:

1. **Type check**: Ensure `tasks` is an array
2. **Required fields**: Verify `id`, `text`, `completed`, `priority` exist
3. **Priority validation**: Check priority is valid value, default to `'medium'` if invalid
4. **Backward compatibility**: If `priority` missing, trigger migration

### Validation on Save

When saving todos to localStorage:

1. **Priority normalization**: Convert to lowercase before save
2. **Priority validation**: Reject invalid priorities with console warning
3. **Quota handling**: Catch `QuotaExceededError` and alert user

---

## Performance Considerations

### Filtering Performance

**Scale**: 100-500 todos (typical use case)

**Operations**:
- Filter by priority: O(n) linear scan
- Expected time for 500 todos: ~5ms (negligible)

**No optimization needed**: JavaScript array filtering is fast enough for target scale

### Storage Size

**Impact**: Adding `priority` field increases storage by ~15-20 bytes per todo

**Example**:
- 100 todos: ~2KB additional storage
- 500 todos: ~10KB additional storage
- localStorage limit: 5-10MB (plenty of headroom)

---

## Example Data Scenarios

### Scenario 1: New User (No Existing Todos)

**Initial State**:
```json
localStorage.todoAppTasks: null
```

**After Creating First Todo**:
```json
[
  {
    "id": 1728936000000,
    "text": "My first task",
    "completed": false,
    "dueDate": null,
    "priority": "medium"
  }
]
```

### Scenario 2: Existing User (Migration)

**Before Migration** (Schema v2):
```json
[
  {
    "id": 1728936000000,
    "text": "Old task",
    "completed": false,
    "dueDate": "2025-10-15"
  }
]
```

**After Migration** (Schema v3):
```json
[
  {
    "id": 1728936000000,
    "text": "Old task",
    "completed": false,
    "dueDate": "2025-10-15",
    "priority": "medium"
  }
]
```

### Scenario 3: Mixed Priority Todos with Filters Active

**Stored Data**:
```json
[
  {"id": 1, "text": "Urgent meeting prep", "completed": false, "dueDate": "2025-10-15", "priority": "high"},
  {"id": 2, "text": "Review document", "completed": false, "dueDate": "2025-10-16", "priority": "medium"},
  {"id": 3, "text": "Read article", "completed": false, "dueDate": null, "priority": "low"},
  {"id": 4, "text": "Completed urgent task", "completed": true, "dueDate": "2025-10-14", "priority": "high"}
]
```

**Filter State**:
```javascript
{
  currentFilter: 'active',              // Show only incomplete todos
  currentDueDateFilter: 'all',          // Show all due date statuses
  currentPriorityFilter: 'high',        // Show only high priority
  currentSort: 'none'                   // No sorting
}
```

**Filtered Result**:
```json
[
  {"id": 1, "text": "Urgent meeting prep", "completed": false, "dueDate": "2025-10-15", "priority": "high"}
]
```

*Explanation*: Only todo #1 passes all filters (active=true, priority=high). Todo #4 is filtered out (completed=true).

---

## Summary

**Schema Changes**:
- ✅ Add `priority` field to Todo entity (string: 'high'|'medium'|'low')
- ✅ Add `currentPriorityFilter` to FilterState (string: 'all'|'high'|'medium'|'low')
- ✅ Migration function adds `priority: 'medium'` to existing todos

**Validation**:
- ✅ Priority must be one of three valid values
- ✅ Invalid/missing priorities default to 'medium'
- ✅ Case-insensitive with lowercase normalization

**Storage**:
- ✅ Priority stored in localStorage with todos
- ✅ Priority filter stored in sessionStorage (session-only persistence)
- ✅ Minimal storage impact (~15-20 bytes per todo)

**Performance**:
- ✅ O(n) filtering adequate for target scale (100-500 todos)
- ✅ No optimization needed
