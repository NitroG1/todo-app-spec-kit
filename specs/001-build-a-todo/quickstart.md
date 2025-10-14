# Quickstart Guide: Todo List Application

**Feature**: Todo List Application
**Date**: 2025-10-11
**Purpose**: Setup, development, and testing guide

## Overview

This guide provides step-by-step instructions for setting up, developing, and testing the todo list application. It serves as both a developer guide and a manual test script.

---

## Prerequisites

### Required
- Modern web browser (Chrome, Firefox, Safari, or Edge)
- Text editor (VS Code, Sublime Text, Notepad++, or any code editor)
- Basic understanding of HTML, CSS, and JavaScript

### Optional
- Live Server extension (VS Code) for auto-reload during development
- Browser DevTools knowledge for debugging

---

## Project Setup

### Step 1: Create Project File

1. Navigate to your project directory:
   ```bash
   cd todo-app
   ```

2. Create the main HTML file:
   ```bash
   # Windows (PowerShell)
   New-Item -Path "index.html" -ItemType File

   # macOS/Linux
   touch index.html
   ```

3. Open `index.html` in your text editor

---

### Step 2: Basic HTML Structure

Start with this basic HTML template:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Todo List Application</title>
  <style>
    /* CSS will go here */
  </style>
</head>
<body>
  <main>
    <h1>Todo List</h1>
    <!-- HTML structure will go here -->
  </main>

  <script>
    // JavaScript will go here
  </script>
</body>
</html>
```

---

## Development Workflow

### Option 1: Direct File Opening

1. Open `index.html` directly in your browser:
   - **Windows**: Double-click the file, or drag to browser
   - **macOS**: Open with browser from Finder
   - **Linux**: Right-click → Open With → Browser

2. After making changes, refresh the browser (`Ctrl+R` or `Cmd+R`)

### Option 2: Live Server (Recommended)

1. Install Live Server in VS Code:
   - Extensions panel → Search "Live Server" → Install

2. Right-click `index.html` → "Open with Live Server"

3. Browser opens automatically; auto-refreshes on file save

---

## Implementation Guide

Follow these phases in order (detailed tasks will be in `tasks.md` after running `/speckit.tasks`):

### Phase 1: HTML Structure
- Create form with input and submit button
- Create filter buttons (All, Active, Completed)
- Create empty task list container (`<ul>`)
- Add ARIA live region for screen reader announcements

### Phase 2: CSS Styling
- Basic reset and layout styles
- Input and button styles
- Task list item styles
- Completed task styles (strikethrough)
- Filter button active states
- Accessibility: focus indicators, contrast

### Phase 3: JavaScript Core Functions
- State variables (`tasks`, `currentFilter`)
- Utility functions (`generateTaskId`, `isValidTaskText`, `escapeHTML`)
- Storage functions (`saveTasks`, `loadTasks`, `saveFilter`, `loadFilter`)

### Phase 4: Task Management
- `addTask()` function
- `deleteTask()` function
- `toggleTaskComplete()` function

### Phase 5: Filtering
- `setFilter()` function
- `getFilteredTasks()` function

### Phase 6: Rendering
- `renderTasks()` function
- `renderFilterButtons()` function

### Phase 7: Event Handlers
- Form submit handler
- Checkbox change handler
- Delete button handler
- Filter button handlers

### Phase 8: Initialization
- `init()` function
- DOMContentLoaded listener

---

## Testing Guide

### Manual Test Script

Use this script to verify all functionality before considering the feature complete.

#### Test 1: Create Tasks ✅

**User Story**: Create and View Tasks (P1)

1. Open the application in a browser
2. Type "Buy groceries" in the input field
3. Click "Add" button (or press Enter)
4. **Expected**: Task appears in the list with unchecked checkbox
5. **Expected**: Input field is cleared and ready for next task
6. Add two more tasks: "Walk the dog" and "Finish homework"
7. **Expected**: All three tasks visible in order added

**Pass Criteria**:
- [ ] Tasks appear immediately after submission
- [ ] Input clears after adding task
- [ ] Empty input does not create task
- [ ] Long task text (500+ chars) is handled correctly

---

#### Test 2: Mark Tasks Complete ✅

**User Story**: Mark Tasks Complete (P2)

1. Click the checkbox next to "Buy groceries"
2. **Expected**: Task text shows strikethrough or visual completion indicator
3. **Expected**: Checkbox is checked
4. Click the checkbox again
5. **Expected**: Task returns to normal appearance
6. **Expected**: Checkbox is unchecked

**Pass Criteria**:
- [ ] Completion state toggles correctly
- [ ] Visual indicator is clear and accessible
- [ ] Only the clicked task changes state
- [ ] State persists after page refresh

---

#### Test 3: Delete Tasks ✅

**User Story**: Delete Tasks (P3)

1. Click the delete button next to "Walk the dog"
2. **Expected**: Task immediately disappears from list
3. **Expected**: Other tasks remain unchanged
4. Refresh the page
5. **Expected**: Deleted task does not reappear

**Pass Criteria**:
- [ ] Task is removed immediately
- [ ] No errors in browser console
- [ ] Other tasks unaffected
- [ ] Deletion persists after refresh

---

#### Test 4: Filter Tasks ✅

**User Story**: Filter Tasks by Status (P4)

**Setup**: Ensure you have mix of completed and active tasks
- Add 3 new tasks if needed
- Mark 2 tasks as complete
- Leave at least 2 tasks active

**Test Steps**:

1. **All Filter** (default):
   - **Expected**: All tasks visible (both completed and active)

2. Click "Active" filter button:
   - **Expected**: Only uncompleted tasks visible
   - **Expected**: Completed tasks hidden
   - **Expected**: "Active" button appears highlighted/active

3. Click "Completed" filter button:
   - **Expected**: Only completed tasks visible
   - **Expected**: Active tasks hidden
   - **Expected**: "Completed" button appears highlighted/active

4. Click "All" filter button:
   - **Expected**: All tasks visible again
   - **Expected**: "All" button appears highlighted/active

5. With "Active" filter selected, mark a task complete:
   - **Expected**: Task disappears from view (no longer matches filter)

6. Switch to "Completed" filter:
   - **Expected**: The task you just completed now appears

**Pass Criteria**:
- [ ] Filters show correct subset of tasks
- [ ] Filter buttons show active state clearly
- [ ] Filtering is instant (no delay)
- [ ] Filter state persists after page refresh

---

#### Test 5: Persistent Storage ✅

**User Story**: Persistent Storage (P1)

1. Add 3-5 tasks with different completion states
2. Set a specific filter (e.g., "Active")
3. Note the exact tasks and their states
4. Close the browser tab completely
5. Open the application again in a new tab
6. **Expected**: All tasks present exactly as before
7. **Expected**: Task completion states preserved
8. **Expected**: Filter selection preserved

**Pass Criteria**:
- [ ] All tasks restored correctly
- [ ] Completion states preserved
- [ ] Task order preserved
- [ ] Filter selection preserved
- [ ] Works after browser restart

---

### Edge Case Testing

#### Test 6: Empty Input Handling

1. Try to submit with empty input field
2. **Expected**: No task created
3. Type only spaces "     " and submit
4. **Expected**: No task created

**Pass**: ✅ / ❌

---

#### Test 7: Long Task Text

1. Enter a very long task description (500+ characters)
2. **Expected**: Task created successfully
3. **Expected**: Text displays without breaking layout
4. Try 1000+ character text
5. **Expected**: Either accepted or gracefully limited

**Pass**: ✅ / ❌

---

#### Test 8: Special Characters

1. Create task with special characters: `<script>alert('test')</script>`
2. **Expected**: Characters are escaped, no script execution
3. Create task with emojis: "Buy 🍕 and 🍔"
4. **Expected**: Emojis display correctly

**Pass**: ✅ / ❌

---

#### Test 9: Many Tasks

1. Add 50+ tasks (can use a loop in console if available)
2. **Expected**: All tasks render without performance issues
3. Test filtering with many tasks
4. **Expected**: Filters work smoothly

**Pass**: ✅ / ❌

---

#### Test 10: localStorage Disabled

1. Open browser in private/incognito mode (or disable localStorage in DevTools)
2. Open the application
3. **Expected**: Warning message about data not persisting (optional)
4. **Expected**: App still works, but data not saved
5. Add tasks and close tab
6. **Expected**: Tasks gone when reopening (expected behavior)

**Pass**: ✅ / ❌

---

### Accessibility Testing

#### Test 11: Keyboard Navigation ✅

**Required for WCAG AA compliance**

1. Open application and press `Tab` key repeatedly
2. **Expected**: Focus moves through all interactive elements in logical order:
   - Input field
   - Add button
   - Filter buttons (All, Active, Completed)
   - Each task's checkbox
   - Each task's delete button

3. While focused on input field, press `Enter`
4. **Expected**: Form submits (same as clicking Add button)

5. Tab to a checkbox, press `Space`
6. **Expected**: Checkbox toggles completion state

7. Tab to delete button, press `Enter` or `Space`
8. **Expected**: Task is deleted

**Pass Criteria**:
- [ ] All interactive elements reachable via keyboard
- [ ] Focus order is logical (top to bottom, left to right)
- [ ] Currently focused element is clearly visible (focus indicator)
- [ ] Enter/Space keys work on appropriate elements
- [ ] No keyboard traps (can always tab out)

---

#### Test 12: Screen Reader Announcements

**Tools**: NVDA (Windows), JAWS (Windows), VoiceOver (macOS/iOS)

1. Enable screen reader
2. Navigate through the application
3. **Expected**: All elements have proper labels
4. **Expected**: Buttons announce their purpose
5. Add a task
6. **Expected**: Screen reader announces task was added
7. Delete a task
8. **Expected**: Screen reader announces task was removed
9. Toggle task completion
10. **Expected**: Screen reader announces state change

**Pass Criteria**:
- [ ] All interactive elements have accessible names
- [ ] ARIA live region announces changes
- [ ] Form labels are properly associated
- [ ] Button purposes are clear

---

#### Test 13: Color Contrast

**Tools**: Browser DevTools, WCAG Contrast Checker, or axe DevTools extension

1. Check text color against background color
2. **Expected**: Contrast ratio ≥ 4.5:1 for normal text
3. **Expected**: Contrast ratio ≥ 3:1 for large text (18pt+)
4. Check button colors
5. Check focus indicator visibility

**Pass Criteria**:
- [ ] All text meets WCAG AA contrast requirements
- [ ] Focus indicators are clearly visible
- [ ] Completion state not indicated by color alone

---

### Browser Compatibility Testing

Test the application in multiple browsers:

- [ ] **Chrome** (latest): All features work
- [ ] **Firefox** (latest): All features work
- [ ] **Safari** (latest): All features work
- [ ] **Edge** (latest): All features work

**Note**: IE11 support optional (localStorage available but ES6 features may need transpiling)

---

## Debugging Tips

### Common Issues and Solutions

#### Issue: Tasks not persisting
**Solution**: Check browser console for localStorage errors. Verify browser allows localStorage (not in private mode).

#### Issue: Tasks not rendering
**Solution**: Check console for JavaScript errors. Verify `renderTasks()` is called after `loadTasks()`.

#### Issue: Filter buttons not working
**Solution**: Verify event listeners are attached. Check `setFilter()` calls `renderTasks()`.

#### Issue: Delete button not working
**Solution**: Ensure delete button event listeners are attached when rendering tasks. Check `taskId` is correctly passed.

### Browser DevTools

**Open DevTools**:
- Windows/Linux: `F12` or `Ctrl+Shift+I`
- macOS: `Cmd+Option+I`

**Useful Panels**:
- **Console**: See errors and log messages
- **Elements**: Inspect HTML/CSS
- **Application** → **Storage** → **Local Storage**: View stored data
- **Network**: Check if any unexpected requests (should be none)

**Debugging localStorage**:
```javascript
// In browser console
console.log(localStorage.getItem('todoAppTasks'));
console.log(localStorage.getItem('todoAppFilter'));

// Clear all data (reset)
localStorage.clear();
```

---

## Performance Validation

### Load Time Test

1. Open browser DevTools → Network tab
2. Hard refresh page (`Ctrl+Shift+R` or `Cmd+Shift+R`)
3. Check total load time in Network panel
4. **Expected**: < 1 second

### Operation Speed Test

1. Add 100+ tasks
2. Test these operations:
   - Add task: **Expected** < 100ms
   - Delete task: **Expected** < 100ms
   - Toggle complete: **Expected** < 100ms
   - Change filter: **Expected** < 50ms

**Pass Criteria**:
- [ ] No perceptible lag on any operation
- [ ] Smooth interaction with 100+ tasks

---

## Deployment

### Option 1: GitHub Pages (Free)

1. Create GitHub repository
2. Push `index.html` to repository
3. Go to repository Settings → Pages
4. Select branch (main/master) and folder (root)
5. Save and wait for deployment
6. Access at `https://yourusername.github.io/repository-name/`

### Option 2: Netlify (Free)

1. Sign up at netlify.com
2. Drag and drop your `index.html` file
3. Get instant URL: `https://random-name.netlify.app/`

### Option 3: Local Network Sharing

1. Use VS Code Live Server
2. Find your local IP (`ipconfig` on Windows, `ifconfig` on macOS/Linux)
3. Share URL: `http://your-ip:5500/index.html`
4. Others on same network can access

---

## Success Checklist

Before considering the feature complete, ensure:

### Functionality
- [ ] All 5 user stories pass tests
- [ ] All edge cases handled gracefully
- [ ] No console errors during normal operation

### Accessibility
- [ ] Full keyboard navigation works
- [ ] Screen reader announces all changes
- [ ] Color contrast meets WCAG AA
- [ ] Focus indicators clearly visible

### Quality
- [ ] Code is well-commented (per constitution)
- [ ] Functions are simple and clear (per constitution)
- [ ] No unnecessary complexity (per constitution)
- [ ] Only approved ES6+ features used (per constitution)

### Performance
- [ ] Page loads in < 1 second
- [ ] All operations feel instant
- [ ] Works smoothly with 100+ tasks

### Constitution Compliance
- [ ] Principle I: Code is beginner-friendly
- [ ] Principle II: Comprehensive comments added
- [ ] Principle III: Only approved JavaScript features
- [ ] Principle IV: WCAG AA accessibility met
- [ ] Principle V: Minimal, clean design

---

## Next Steps

After completing and testing the implementation:

1. **Documentation**: Update root README.md with usage instructions
2. **Screenshots**: Take screenshots for README
3. **Deployment**: Deploy to hosting service
4. **User Testing**: Have others test the application
5. **Iteration**: Gather feedback and refine

---

## Support and Resources

### Documentation
- [spec.md](./spec.md) - Feature requirements
- [plan.md](./plan.md) - Implementation plan
- [data-model.md](./data-model.md) - Data structures
- [contracts/function-interfaces.md](./contracts/function-interfaces.md) - Function definitions

### External Resources
- MDN Web Docs: https://developer.mozilla.org/
- localStorage API: https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
- WCAG Guidelines: https://www.w3.org/WAI/WCAG21/quickref/
- ARIA Practices: https://www.w3.org/WAI/ARIA/apg/

### Accessibility Testing Tools
- axe DevTools (browser extension)
- WAVE (browser extension)
- Lighthouse (built into Chrome DevTools)
- NVDA Screen Reader (free, Windows)
- VoiceOver (built into macOS/iOS)

---

## Version History

- **v1.0** (2025-10-11): Initial quickstart guide created
