# Research: Add Due Dates to Todos

**Feature**: 002-add-due-dates
**Date**: 2025-10-14
**Purpose**: Document technical research and decisions for implementing due date functionality

This document captures research decisions and rationale for extending the todo list application with due date support. All decisions prioritize simplicity, beginner-friendliness, and constitutional compliance.

## Decision 1: Date Storage Format

**Decision**: ISO 8601 date string (YYYY-MM-DD format)

**Rationale**:
1. **Lexicographic sorting**: ISO 8601 strings sort correctly using native JavaScript string comparison (`"2025-01-15" < "2025-02-20"` works naturally)
2. **Human-readable**: Easy to debug in localStorage; developers can see `"2025-10-14"` instead of `1728864000000`
3. **Cross-browser compatibility**: String format works identically across all browsers
4. **Native support**: HTML `<input type="date">` reads/writes ISO 8601 format natively
5. **Minimal storage**: ~10 bytes per date (e.g., "2025-10-14")
6. **Beginner-friendly**: Easier to understand than timestamps for learners

**Alternatives Considered**:
- **Unix timestamp**: More compact (number), but not human-readable and requires conversion for display
- **Locale-specific strings**: Breaks sorting (e.g., "10/14/2025" vs "14/10/2025" sort incorrectly)
- **Date object serialization**: Requires JSON.parse with custom reviver, adds complexity

**Implementation Notes**:
```javascript
// Simple ISO 8601 usage
const today = new Date().toISOString().split('T')[0]; // "2025-10-14"
const dueDate = document.querySelector('input[type="date"]').value; // "2025-10-14"

// Sorting works natively
tasks.sort((a, b) => {
  if (!a.dueDate) return 1; // null dates go last
  if (!b.dueDate) return -1;
  return a.dueDate.localeCompare(b.dueDate); // or simple < comparison
});
```

## Decision 2: Edit Mode Interaction Pattern

**Decision**: Inline edit with pencil/edit icon next to each todo

**Rationale**:
1. **Context preservation**: User stays in the todo list view; no navigation or modal disruption
2. **Visual clarity**: Edit icon clearly indicates "this todo can be edited"
3. **Accessibility**: Icon button can have proper ARIA label ("Edit due date for [task name]")
4. **Simple implementation**: Show/hide date picker on icon click (simple DOM toggle)
5. **Familiar pattern**: Matches common UX patterns (e.g., GitHub inline comments, Trello card editing)
6. **Keyboard accessible**: Tab to icon, Enter/Space to activate

**Alternatives Considered**:
- **Click todo itself**: Ambiguous—user might expect to view/complete, not edit
- **Double-click todo**: Not discoverable; requires explanation; not accessible
- **Modal dialog**: Breaks context; adds complexity (overlay, focus trap, ESC handling)

**Implementation Notes**:
```javascript
// Each todo item gets an edit icon button
<button class="edit-btn" aria-label="Edit due date">✏️</button>

// Click reveals inline date picker
editBtn.addEventListener('click', () => {
  datePickerContainer.style.display = 'block';
  dateInput.focus(); // Keyboard accessible
});
```

## Decision 3: Visual Indicator Design

**Decision**: Color + icon combination for overdue/due-today todos

**Rationale**:
1. **Accessibility (WCAG AA)**: Color alone fails for colorblind users; dual-coding (color + icon) ensures information is conveyed through multiple channels
2. **Constitutional compliance**: Principle IV requires "Color MUST NOT be the only means of conveying information"
3. **Immediate recognition**: Icons (⚠️ for overdue, 📅 for today) provide instant visual cues
4. **Screen reader support**: Icons can have ARIA labels ("Warning: Task is overdue")
5. **Beginner-friendly**: Simple CSS classes + text/emoji icons; no complex rendering

**Alternatives Considered**:
- **Color-only**: Violates WCAG AA and constitution; fails for colorblind users
- **Text badges only**: Works but less visually intuitive than icons
- **Border styling only**: Subtle but may not be immediately noticeable

**Implementation Notes**:
```javascript
// CSS classes for dual-coded indicators
.todo.overdue {
  background-color: #fee; // Light red background
  border-left: 3px solid #c00; // Red accent border
}

// Icon + ARIA label
<span class="overdue-icon" aria-label="Warning: Task is overdue">⚠️</span>

// Color choices (WCAG AA compliant)
- Overdue: Red (#c00) on light background (#fee), contrast ratio > 4.5:1
- Due today: Orange/amber (#f90) on light background (#ffedd5), contrast ratio > 4.5:1
- Future: Neutral gray (no special indicator)
```

## Decision 4: Sort and Filter Control Placement

**Decision**: Toolbar above the todo list

**Rationale**:
1. **Discoverability**: Controls visible without scrolling; users expect filters at the top
2. **Visual hierarchy**: Clear separation between controls and content
3. **Familiar pattern**: Matches email clients (Gmail), file managers (Finder/Explorer), e-commerce filters
4. **Keyboard navigation**: Tab order naturally flows from toolbar → todo list
5. **Responsive-friendly**: Toolbar can stack/wrap on narrow screens
6. **Beginner-friendly**: Standard HTML buttons and dropdowns; no custom widgets

**Alternatives Considered**:
- **Sidebar panel**: Adds horizontal space complexity; less common pattern
- **Floating action button (FAB)**: Hides controls; requires additional interaction; not keyboard-discoverable
- **Below input form**: Breaks natural flow (input → filters → results)

**Implementation Notes**:
```html
<div class="toolbar">
  <label for="sort-select">Sort:</label>
  <select id="sort-select" aria-label="Sort todos by">
    <option value="none">None</option>
    <option value="due-asc">Due Date (Earliest First)</option>
    <option value="due-desc">Due Date (Latest First)</option>
  </select>

  <label>Filter:</label>
  <div class="filter-buttons" role="group" aria-label="Filter todos by due date">
    <button data-filter="all">All</button>
    <button data-filter="overdue">Overdue</button>
    <button data-filter="today">Due Today</button>
    <button data-filter="week">This Week</button>
    <button data-filter="none">No Due Date</button>
  </div>
</div>
```

## Decision 5: Data Migration Strategy

**Decision**: Automatic graceful upgrade on first load (add `dueDate: null` to todos missing the field)

**Rationale**:
1. **Zero user action**: Migration happens transparently; users don't need to "upgrade" manually
2. **Backward compatibility**: Old todos work immediately with new code
3. **Beginner-friendly**: Simple check-and-add logic; no complex migration scripts
4. **Safe**: Non-destructive operation (only adds missing field, never modifies existing data)
5. **One-time cost**: Migration runs once per browser/user when new code first loads

**Alternatives Considered**:
- **Lazy migration (check at render)**: Works but repeats check every render (wasteful)
- **Full data reset**: Unacceptable; loses user data
- **Manual migration button**: Requires user action; poor UX; easy to forget

**Implementation Notes**:
```javascript
// Run once on app initialization
function migrateTasksSchema() {
  const tasks = loadTasksFromStorage();
  let updated = false;

  tasks.forEach(task => {
    // Add dueDate field if missing
    if (!task.hasOwnProperty('dueDate')) {
      task.dueDate = null;
      updated = true;
    }
  });

  // Save back to localStorage if any tasks were updated
  if (updated) {
    saveTasksToStorage(tasks);
    console.log('Migrated tasks to include dueDate field');
  }
}

// Call during app initialization (after DOM loaded)
document.addEventListener('DOMContentLoaded', () => {
  migrateTasksSchema(); // Run migration first
  loadAndRenderTasks(); // Then load/render as usual
});
```

## Decision 6: Date Comparison and Status Logic

**Decision**: Use JavaScript Date objects for comparison, store as ISO 8601 strings

**Rationale**:
1. **Accurate comparison**: Date objects handle timezone and DST correctly
2. **Simple implementation**: `new Date(isoString)` converts for comparison
3. **Today detection**: Compare dates at day granularity (ignore time)
4. **Browser compatibility**: Date API well-supported in all target browsers

**Implementation Pattern**:
```javascript
/**
 * Get the status of a due date relative to today
 * @param {string|null} dueDateString - ISO 8601 date string (YYYY-MM-DD) or null
 * @returns {string} - 'overdue', 'today', 'future', or 'none'
 */
function getDueDateStatus(dueDateString) {
  if (!dueDateString) return 'none';

  // Get today's date at midnight (ignore time of day)
  const today = new Date();
  today.setHours(0, 0, 0, 0);

  // Parse due date (also at midnight)
  const dueDate = new Date(dueDateString + 'T00:00:00');

  // Compare dates
  if (dueDate < today) return 'overdue';
  if (dueDate.toDateString() === today.toDateString()) return 'today';
  return 'future';
}
```

## Decision 7: Date Picker Implementation

**Decision**: Use native HTML `<input type="date">`

**Rationale**:
1. **Zero dependencies**: No JavaScript date picker library needed
2. **Accessibility built-in**: Native controls are keyboard and screen reader accessible
3. **Mobile-friendly**: Browsers provide appropriate UI (calendar on mobile, dropdown on desktop)
4. **Localization**: Browser respects user's locale for date format display
5. **Simple integration**: Value is already in ISO 8601 format
6. **Constitutional compliance**: Aligns with Principle V (minimal dependencies)

**Alternatives Considered**:
- **Custom JavaScript date picker**: Adds complexity; requires custom accessibility implementation
- **Third-party library (Flatpickr, Pikaday)**: Violates zero-dependency constraint

**Browser Support**:
- Chrome 20+, Firefox 57+, Safari 14.1+, Edge 12+ all support `<input type="date">`
- Fallback: On older browsers, degrades to text input (user can type "YYYY-MM-DD")

**Implementation Notes**:
```html
<input
  type="date"
  id="todo-due-date"
  aria-label="Due date"
  min="2020-01-01"
  max="2099-12-31"
/>
```

## Decision 8: Filter "Due This Week" Definition

**Decision**: Next 7 days from today (inclusive of today)

**Rationale**:
1. **Intuitive**: "This week" = next 7 days feels natural
2. **Implementation simplicity**: Add 7 days to today, compare dates
3. **Consistent with "Due Today"**: Both use calendar days, not hours
4. **Predictable**: Always shows same time span regardless of day of week

**Alternatives Considered**:
- **Sunday-Saturday week**: Ambiguous (current week? next Sunday?); implementation complexity
- **Monday-Sunday week**: Same issues as above

**Implementation Notes**:
```javascript
function isWithinNextWeek(dueDateString) {
  if (!dueDateString) return false;

  const today = new Date();
  today.setHours(0, 0, 0, 0);

  const nextWeek = new Date(today);
  nextWeek.setDate(today.getDate() + 7);

  const dueDate = new Date(dueDateString + 'T00:00:00');

  return dueDate >= today && dueDate <= nextWeek;
}
```

## Summary of Key Decisions

| Decision Area | Choice | Primary Rationale |
|---------------|--------|-------------------|
| Storage Format | ISO 8601 (YYYY-MM-DD) | Lexicographic sorting + human-readable |
| Edit Interaction | Inline edit with icon | Context preservation + accessibility |
| Visual Indicators | Color + icon combination | WCAG AA compliance (dual-coding) |
| Control Placement | Toolbar above list | Discoverability + familiar pattern |
| Data Migration | Automatic on load | Zero user action + backward compatibility |
| Date Comparison | Date objects | Accurate + handles timezone/DST |
| Date Picker | Native HTML input | Zero dependencies + built-in accessibility |
| "This Week" Definition | Next 7 days | Intuitive + simple implementation |

All decisions documented here support the constitutional principles and meet the feature requirements specified in spec.md.
