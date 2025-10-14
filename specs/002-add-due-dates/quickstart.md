# Quickstart Guide: Add Due Dates to Todos

**Feature**: 002-add-due-dates
**Date**: 2025-10-14
**Purpose**: Step-by-step implementation and testing guide

This guide provides a structured approach to implementing and testing the due date feature. Follow each phase sequentially to ensure proper functionality and constitutional compliance.

## Implementation Overview

This feature extends the existing `index.html` file from feature 001-build-a-todo with:
1. Extended Todo data model (adds `dueDate` field)
2. Data migration logic (adds `dueDate: null` to existing todos)
3. Toolbar HTML above todo list (sort and filter controls)
4. Inline edit controls (edit icon + date picker)
5. Visual indicators (color + icon for overdue/today)
6. CSS for toolbar, date display, and indicators
7. JavaScript functions for date operations, sorting, and filtering

---

## Phase 1: Data Model Extension and Migration

### Step 1.1: Extend Todo Data Structure

**Objective**: Add `dueDate` field to Todo objects

**Action**:
- Locate the `createTask()` function in `index.html <script>` section
- Add `dueDate: null` to the returned todo object

**Expected**:
```javascript
function createTask(text) {
  return {
    id: Date.now(),
    text: text,
    completed: false,
    dueDate: null  // NEW: Add this field
  };
}
```

### Step 1.2: Implement Data Migration

**Objective**: Upgrade existing todos to include `dueDate` field

**Action**:
- Add `migrateTasksSchema()` function after `loadTasksFromStorage()`
- Call `migrateTasksSchema()` in the initialization code (inside `DOMContentLoaded`)

**Expected**:
```javascript
function migrateTasksSchema() {
  const tasks = loadTasksFromStorage();
  let updated = false;

  tasks.forEach(task => {
    if (!task.hasOwnProperty('dueDate')) {
      task.dueDate = null;
      updated = true;
    }
  });

  if (updated) {
    saveTasksToStorage(tasks);
    console.log(`Migrated ${tasks.length} todos to include dueDate field`);
  }
}

// In DOMContentLoaded:
document.addEventListener('DOMContentLoaded', () => {
  migrateTasksSchema(); // Run migration first
  loadAndRenderTasks();
});
```

### Test 1.1: Data Migration

**Test Steps**:
1. Open browser DevTools → Application → Local Storage
2. Manually add an old-format todo: `[{"id":1,"text":"Old todo","completed":false}]`
3. Refresh the page
4. Check localStorage again

**Expected Result**:
- Console shows: "Migrated 1 todos to include dueDate field"
- localStorage now shows: `[{"id":1,"text":"Old todo","completed":false,"dueDate":null}]`

---

## Phase 2: Core Due Date Functions

### Step 2.1: Date Status Calculation

**Objective**: Implement `getDueDateStatus()` function

**Action**:
- Add function to calculate if a date is overdue, today, or future

**Expected**:
```javascript
function getDueDateStatus(dueDateString) {
  if (!dueDateString) return 'none';

  const today = new Date();
  today.setHours(0, 0, 0, 0);

  const dueDate = new Date(dueDateString + 'T00:00:00');

  if (dueDate < today) return 'overdue';
  if (dueDate.toDateString() === today.toDateString()) return 'today';
  return 'future';
}
```

### Step 2.2: Set/Remove Due Date Functions

**Objective**: Implement `setTodoDueDate()` and `removeTodoDueDate()`

**Action**:
- Add functions to modify todo due dates

**Expected**:
```javascript
function setTodoDueDate(todoId, dueDateString) {
  const tasks = loadTasksFromStorage();
  const task = tasks.find(t => t.id === todoId);

  if (!task) return false;
  if (!isValidDueDate(dueDateString)) return false;

  task.dueDate = dueDateString;
  saveTasksToStorage(tasks);
  renderTasks();
  announceDueDateChange(`Due date set to ${formatDueDateForDisplay(dueDateString)}`);
  return true;
}

function removeTodoDueDate(todoId) {
  const tasks = loadTasksFromStorage();
  const task = tasks.find(t => t.id === todoId);

  if (!task) return false;

  task.dueDate = null;
  saveTasksToStorage(tasks);
  renderTasks();
  announceDueDateChange('Due date removed');
  return true;
}
```

### Test 2.1: Date Status Logic

**Test Steps**:
1. Open browser console
2. Test with various dates:
   - `getDueDateStatus("2020-01-01")` → should return `"overdue"`
   - `getDueDateStatus(new Date().toISOString().split('T')[0])` → should return `"today"`
   - `getDueDateStatus("2099-12-31")` → should return `"future"`
   - `getDueDateStatus(null)` → should return `"none"`

**Expected Result**:
- All status calculations return correct values

---

## Phase 3: Toolbar UI (Sort and Filter Controls)

### Step 3.1: Add Toolbar HTML

**Objective**: Add toolbar above todo list

**Action**:
- Locate the `<ul id="todo-list">` in the HTML
- Add toolbar div above it

**Expected**:
```html
<!-- Add this BEFORE <ul id="todo-list"> -->
<div class="toolbar" id="toolbar">
  <div class="sort-controls">
    <label for="sort-select">Sort:</label>
    <select id="sort-select" aria-label="Sort todos by">
      <option value="none">None</option>
      <option value="due-asc">Due Date (Earliest First)</option>
      <option value="due-desc">Due Date (Latest First)</option>
    </select>
  </div>

  <div class="filter-controls" role="group" aria-label="Filter todos by due date">
    <label>Filter:</label>
    <button class="filter-btn active" data-filter="all">All</button>
    <button class="filter-btn" data-filter="overdue">Overdue</button>
    <button class="filter-btn" data-filter="today">Due Today</button>
    <button class="filter-btn" data-filter="week">This Week</button>
    <button class="filter-btn" data-filter="none">No Due Date</button>
  </div>
</div>
```

### Step 3.2: Style Toolbar

**Objective**: Add CSS for toolbar layout

**Action**:
- Add styles to `<style>` section

**Expected**:
```css
.toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  background-color: #f8f9fa;
  border: 1px solid #dee2e6;
  border-radius: 8px;
  margin-bottom: 16px;
}

.sort-controls {
  display: flex;
  align-items: center;
  gap: 8px;
}

#sort-select {
  padding: 6px 12px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  font-size: 14px;
}

.filter-controls {
  display: flex;
  align-items: center;
  gap: 8px;
}

.filter-btn {
  padding: 6px 12px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  background-color: white;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.2s;
}

.filter-btn:hover {
  background-color: #e9ecef;
}

.filter-btn.active {
  background-color: #007bff;
  color: white;
  border-color: #007bff;
}
```

### Test 3.1: Toolbar Rendering

**Test Steps**:
1. Open the app in a browser
2. Verify toolbar appears above todo list
3. Check that all buttons and dropdown are visible

**Expected Result**:
- Toolbar displays with sort dropdown and filter buttons
- "All" filter button is active by default

---

## Phase 4: Inline Edit UI (Edit Icon + Date Picker)

### Step 4.1: Add Edit Icon to Each Todo

**Objective**: Add edit button to todo items

**Action**:
- Modify `renderTasks()` or the todo item HTML generation
- Add edit icon button to each todo

**Expected**:
```javascript
// In the function that generates todo HTML:
<li class="todo-item ${task.completed ? 'completed' : ''}">
  <input type="checkbox" ${task.completed ? 'checked' : ''}
         onchange="toggleTask(${task.id})"
         aria-label="Mark task as ${task.completed ? 'incomplete' : 'complete'}">
  <span class="todo-text">${task.text}</span>

  <!-- NEW: Edit icon button -->
  <button class="edit-btn" onclick="toggleDatePicker(${task.id})"
          aria-label="Edit due date">
    ✏️
  </button>

  <!-- NEW: Inline date picker (hidden by default) -->
  <div class="date-picker-container" id="picker-${task.id}" style="display: none;">
    <input type="date" id="date-input-${task.id}"
           value="${task.dueDate || ''}"
           onchange="handleDueDateChange(event, ${task.id})"
           aria-label="Select due date">
    <button onclick="removeTodoDueDate(${task.id})">Clear</button>
  </div>

  <!-- NEW: Due date display -->
  ${task.dueDate ? `<span class="due-date">${formatDueDateForDisplay(task.dueDate)}</span>` : ''}

  <button class="delete-btn" onclick="deleteTask(${task.id})"
          aria-label="Delete task">
    🗑️
  </button>
</li>
```

### Step 4.2: Implement Toggle Date Picker

**Objective**: Show/hide date picker on edit icon click

**Action**:
- Add `toggleDatePicker()` function

**Expected**:
```javascript
function toggleDatePicker(todoId) {
  const picker = document.getElementById(`picker-${todoId}`);
  const dateInput = document.getElementById(`date-input-${todoId}`);

  if (picker.style.display === 'none') {
    picker.style.display = 'block';
    dateInput.focus();
  } else {
    picker.style.display = 'none';
  }
}
```

### Test 4.1: Edit Icon Interaction

**Test Steps**:
1. Create a new todo
2. Click the edit icon (✏️)
3. Verify date picker appears
4. Click edit icon again

**Expected Result**:
- Date picker shows/hides on edit icon click
- Date input receives focus when picker opens

---

## Phase 5: Visual Indicators

### Step 5.1: Add CSS for Visual Indicators

**Objective**: Style overdue and due-today todos

**Action**:
- Add CSS classes for date status

**Expected**:
```css
.todo-item.overdue {
  background-color: #fee;
  border-left: 3px solid #c00;
}

.todo-item.due-today {
  background-color: #ffedd5;
  border-left: 3px solid #f90;
}

.todo-item .status-icon {
  margin-left: 8px;
  font-size: 16px;
}
```

### Step 5.2: Apply Status Classes in Render

**Objective**: Add status-based classes to todo items

**Action**:
- Modify todo rendering to include status class and icon

**Expected**:
```javascript
// In renderTasks(), calculate status:
const status = getDueDateStatus(task.dueDate);
const statusInfo = getDueDateIconAndLabel(status);

// Add class to todo item:
<li class="todo-item ${task.completed ? 'completed' : ''} ${statusInfo.cssClass}">
  <!-- ... todo content ... -->
  ${statusInfo.icon ? `<span class="status-icon" aria-label="${statusInfo.label}">${statusInfo.icon}</span>` : ''}
</li>
```

### Test 5.1: Visual Indicators

**Test Steps**:
1. Create todo with yesterday's date → should show red background + ⚠️
2. Create todo with today's date → should show orange background + 📅
3. Create todo with future date → should show normal appearance

**Expected Result**:
- Overdue todos have red styling and warning icon
- Due-today todos have orange styling and calendar icon
- Future todos have no special styling

---

## Phase 6: Sorting and Filtering

### Step 6.1: Implement Sort Logic

**Objective**: Handle sort dropdown changes

**Action**:
- Add event listener and sort function

**Expected**:
```javascript
document.getElementById('sort-select').addEventListener('change', (e) => {
  const sortValue = e.target.value;
  let tasks = loadTasksFromStorage();

  if (sortValue === 'due-asc') {
    tasks = sortTodosByDueDate(tasks, 'asc');
  } else if (sortValue === 'due-desc') {
    tasks = sortTodosByDueDate(tasks, 'desc');
  }

  renderTasksArray(tasks); // Render sorted array without saving
});
```

### Step 6.2: Implement Filter Logic

**Objective**: Handle filter button clicks

**Action**:
- Add event listeners to filter buttons

**Expected**:
```javascript
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', (e) => {
    // Update active button
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    e.target.classList.add('active');

    // Filter tasks
    const filterValue = e.target.dataset.filter;
    let tasks = loadTasksFromStorage();

    if (filterValue !== 'all') {
      tasks = filterTodosByDueDateCriteria(tasks, filterValue);
    }

    renderTasksArray(tasks);
  });
});
```

### Test 6.1: Sorting

**Test Steps**:
1. Create 3 todos with different due dates (e.g., Oct 10, Oct 20, Oct 15)
2. Select "Due Date (Earliest First)" from sort dropdown
3. Verify todos appear in order: Oct 10, Oct 15, Oct 20
4. Select "Due Date (Latest First)"
5. Verify order reverses: Oct 20, Oct 15, Oct 10

**Expected Result**:
- Sorting works correctly in both directions
- Todos without due dates appear at the end

### Test 6.2: Filtering

**Test Steps**:
1. Create todos with various dates: overdue, today, next week, no date
2. Click "Overdue" filter → only overdue todos show
3. Click "Due Today" → only today's todos show
4. Click "This Week" → todos due within 7 days show
5. Click "No Due Date" → only todos without dates show
6. Click "All" → all todos show again

**Expected Result**:
- Each filter shows correct subset of todos
- "All" filter restores full list

---

## Phase 7: Accessibility Testing

### Test 7.1: Keyboard Navigation

**Test Steps**:
1. Use Tab key to navigate through the app
2. Verify tab order: input → add button → sort dropdown → filter buttons → edit icons → checkboxes → delete buttons
3. Use Enter/Space to activate buttons and controls
4. Use arrow keys in dropdown and date picker

**Expected Result**:
- All interactive elements reachable via keyboard
- Tab order is logical
- All controls operable without mouse

### Test 7.2: Screen Reader Testing

**Test Steps** (using NVDA/JAWS on Windows or VoiceOver on Mac):
1. Navigate to the app with screen reader enabled
2. Verify toolbar controls are announced correctly
3. Click edit icon → verify "Edit due date" is announced
4. Set a due date → verify change is announced in ARIA live region
5. Navigate to overdue todo → verify warning icon is announced

**Expected Result**:
- All interactive elements have proper labels
- Due date changes announced to screen reader
- Status icons have ARIA labels

---

## Phase 8: Constitution Compliance Verification

### Checklist

- [ ] **Principle I: Simplicity**
  - All functions use simple, descriptive names
  - No complex patterns (generators, proxies, decorators)
  - Logic broken into small, single-purpose functions

- [ ] **Principle II: Comments**
  - Every function has comment block with purpose, params, returns
  - Date comparison logic explained step-by-step
  - Migration strategy documented inline

- [ ] **Principle III: Modern JavaScript**
  - Only approved ES6+ features used (const/let, template literals, arrow functions, .filter(), .map(), .sort())
  - No restricted features (.reduce(), generators, proxies)

- [ ] **Principle IV: Accessibility**
  - Color + icon dual-coding for status indicators
  - All controls keyboard accessible
  - ARIA labels on icon-only elements
  - ARIA live region for announcements
  - Native date input for built-in accessibility

- [ ] **Principle V: Minimal Design**
  - Zero new dependencies (no date picker library)
  - Single-file structure maintained
  - Clean CSS organization
  - No unused styles

---

## Final Validation

### End-to-End Test

1. **Setup**: Open app, verify migration runs (check console)
2. **Create**: Add todo with due date
3. **Edit**: Change due date using edit icon
4. **Display**: Verify date shows with correct formatting
5. **Status**: Create overdue/today/future todos, verify visual indicators
6. **Sort**: Sort by due date ascending and descending
7. **Filter**: Test all filter options (overdue, today, week, none, all)
8. **Remove**: Clear due date from a todo
9. **Complete**: Mark todo with due date as complete, verify date persists
10. **Persist**: Refresh page, verify all due dates survived
11. **Keyboard**: Complete all operations using only keyboard
12. **Screen Reader**: Navigate with screen reader, verify announcements

### Success Criteria

- ✅ All tests pass
- ✅ Constitution checklist complete
- ✅ No console errors
- ✅ Keyboard fully functional
- ✅ Screen reader accessible
- ✅ Data persists across sessions
- ✅ Backward compatible with old todos

---

## Troubleshooting

### Common Issues

**Issue**: Old todos don't have due dates after migration
- **Solution**: Check that `migrateTasksSchema()` is called before `loadAndRenderTasks()`

**Issue**: Date picker doesn't show
- **Solution**: Verify `toggleDatePicker()` function and check CSS `display` property

**Issue**: Sorting doesn't work
- **Solution**: Ensure `compareDueDates()` properly handles null values

**Issue**: Visual indicators not showing
- **Solution**: Check that `getDueDateStatus()` is called during rendering and CSS classes are applied

**Issue**: Keyboard navigation broken
- **Solution**: Verify all buttons have proper tabindex and event handlers

---

This quickstart guide provides a complete implementation path for the due date feature while ensuring constitutional compliance at every step.
