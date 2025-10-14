# Research: Todo List Application

**Feature**: Todo List Application
**Date**: 2025-10-11
**Status**: Complete

## Overview

This document captures research decisions and rationale for the technical implementation of the todo list application. All decisions prioritize simplicity, beginner-friendliness, and constitutional compliance.

---

## Decision 1: Vanilla JavaScript (No Framework)

**Decision**: Use vanilla JavaScript with no frameworks or libraries

**Rationale**:
1. **Constitutional Alignment**: Directly supports Principle I (Code Simplicity) and Principle V (Minimal Dependencies)
2. **Beginner Accessibility**: No framework-specific concepts to learn (components, state management, JSX, etc.)
3. **Zero Setup**: No build tools, package managers, or configuration required
4. **Performance**: Minimal overhead, fast load times
5. **Longevity**: No dependency on framework versions or breaking changes

**Alternatives Considered**:
- **React**: Rejected - Adds complexity (JSX, components, hooks) unnecessary for simple CRUD operations
- **Vue**: Rejected - Still requires learning framework concepts, build setup for optimal use
- **jQuery**: Rejected - Adds dependency; modern DOM APIs are sufficient and more educational

**Best Practices**:
- Use modern DOM APIs: `querySelector`, `addEventListener`, `classList`
- Organize code into pure functions for testability
- Use template literals for dynamic HTML generation
- Keep global state minimal and explicit

---

## Decision 2: localStorage for Persistence

**Decision**: Use browser's native localStorage API for data persistence

**Rationale**:
1. **Built-in Solution**: No server or database setup required
2. **Offline-First**: Works without network connection after initial load
3. **Simple API**: Only 5 methods needed (`setItem`, `getItem`, `removeItem`, `clear`, `key`)
4. **Sufficient Capacity**: 5-10MB limit supports thousands of tasks
5. **Wide Browser Support**: Available since IE8, all modern browsers

**Alternatives Considered**:
- **IndexedDB**: Rejected - More complex API, overkill for simple key-value storage
- **Cookies**: Rejected - Limited size (4KB), sent with every request (unnecessary overhead)
- **sessionStorage**: Rejected - Clears on tab close, doesn't meet persistence requirement
- **Server/Database**: Rejected - Adds complexity (backend, auth, hosting), violates simplicity principle

**Best Practices**:
- Serialize data as JSON strings for storage
- Implement error handling for quota exceeded scenarios
- Check for localStorage availability before use
- Use a consistent storage key (e.g., `"todoAppTasks"`)

**localStorage API Reference**:
```javascript
// Save data
localStorage.setItem('key', 'value');

// Retrieve data
const data = localStorage.getItem('key');

// Remove data
localStorage.removeItem('key');

// Clear all data
localStorage.clear();
```

---

## Decision 3: Single HTML File Structure

**Decision**: Combine HTML, CSS, and JavaScript in a single `index.html` file

**Rationale**:
1. **Maximum Simplicity**: No module bundlers, import statements, or file references
2. **Deployment**: Single file can be opened directly in browser or hosted anywhere
3. **Beginner-Friendly**: Complete application visible in one file, easy to understand data flow
4. **No Build Step**: Edit and refresh - instant feedback loop
5. **Constitutional Compliance**: Aligns with Principle V (Minimal & Clean Design)

**Alternatives Considered**:
- **Separate Files**: Rejected for MVP - Adds complexity in file management and loading order
- **Module System**: Rejected - Requires build tools or server for ES modules
- **Build Pipeline**: Rejected - Violates simplicity principle for a small application

**Note**: For a larger production application, separation into multiple files would be appropriate. This single-file approach is optimal for learning and this specific scope.

---

## Decision 4: Semantic HTML for Accessibility

**Decision**: Use semantic HTML5 elements with proper ARIA attributes

**Rationale**:
1. **Constitutional Requirement**: Principle IV mandates WCAG AA compliance
2. **Screen Reader Support**: Proper semantics provide context for assistive technologies
3. **Keyboard Navigation**: Semantic elements have built-in keyboard support
4. **SEO Benefits**: Better document structure understanding
5. **Code Clarity**: Element names convey purpose

**HTML Elements to Use**:
- `<main>` - Main content area
- `<form>` - Task input form
- `<label>` - Associate text with input field
- `<input>` - Task text entry
- `<button>` - Action triggers (add, delete, filter)
- `<ul>` - Unordered list of tasks
- `<li>` - Individual task items
- `<input type="checkbox">` - Task completion toggle

**ARIA Attributes to Use**:
- `aria-label` - Descriptive labels for icon-only buttons (delete)
- `aria-live="polite"` - Announce task additions/removals to screen readers
- `role="status"` - For live region announcements

**Best Practices**:
- Every form input has an associated `<label>`
- Buttons use descriptive text or aria-label
- Focus states are visible
- Logical tab order maintained

---

## Decision 5: CSS Approach (Inline Styles)

**Decision**: Use `<style>` tag in HTML head with organized, minimal CSS

**Rationale**:
1. **Single File Benefit**: Keeps everything self-contained
2. **No External Dependencies**: No CSS frameworks or preprocessors
3. **Simple Organization**: Clear sections (reset, layout, components, states)
4. **Performance**: No additional HTTP requests

**CSS Organization Structure**:
```css
/* 1. Reset/Base styles */
/* 2. Layout */
/* 3. Components (input, buttons, tasks) */
/* 4. States (completed, filters) */
/* 5. Accessibility (focus states, high contrast) */
```

**Alternatives Considered**:
- **CSS Framework (Bootstrap, Tailwind)**: Rejected - Violates zero-dependency principle
- **External CSS File**: Rejected - Adds file management complexity
- **Inline Styles (style attribute)**: Rejected - Harder to maintain, no reusability

**Accessibility CSS Requirements**:
- Minimum color contrast ratio 4.5:1 for text (WCAG AA)
- Visible focus indicators (outline or custom style)
- Sufficient touch target size (44x44px minimum)
- Responsive text sizing

---

## Decision 6: ES6+ Feature Subset

**Decision**: Use approved modern JavaScript features only

**Approved Features** (per Constitution Principle III):
- `const` and `let` - Block-scoped variables
- Arrow functions - For event handlers and callbacks
- Template literals - For dynamic HTML strings
- Array methods - `.filter()`, `.map()`, `.forEach()`
- Destructuring (simple) - For array/object access when clear

**Avoided Features**:
- Generators, Proxies, Symbols - Too advanced for beginners
- `.reduce()` - Complex for beginners, will use simple loops
- Complex destructuring - Can be confusing
- Classes - Using object literals and functions instead

**Example Code Pattern**:
```javascript
// Good: Simple, clear function
function addTask(taskText) {
  const task = {
    id: Date.now(),
    text: taskText,
    completed: false
  };
  tasks.push(task);
  saveTasks();
  renderTasks();
}

// Avoid: Complex patterns
const addTask = (text) => ({id: Date.now(), text, completed: false}) |> tasks.push;
```

---

## Decision 7: Data Model Structure

**Decision**: Use array of task objects with simple properties

**Task Object Structure**:
```javascript
{
  id: 1234567890,        // Unique identifier (timestamp)
  text: "Buy groceries", // Task description
  completed: false       // Completion status
}
```

**Rationale**:
1. **Simplicity**: Flat structure, no nesting
2. **JSON Compatible**: Direct serialization for localStorage
3. **Sufficient Properties**: Meets all requirements with minimal data
4. **Timestamp ID**: Simple, collision-resistant for single-user app

**Alternatives Considered**:
- **UUID for ID**: Rejected - Requires library or complex generation code
- **Auto-increment ID**: Rejected - Requires state management for counter
- **Additional Metadata** (created date, priority, categories): Rejected - Not in requirements, violates YAGNI

---

## Decision 8: Filter Implementation

**Decision**: Client-side filtering using array `.filter()` method

**Filter Types**:
- **All**: Show all tasks (no filtering)
- **Active**: Show tasks where `completed === false`
- **Completed**: Show tasks where `completed === true`

**Implementation Approach**:
```javascript
function renderTasks() {
  let filteredTasks = tasks;

  if (currentFilter === 'active') {
    filteredTasks = tasks.filter(task => !task.completed);
  } else if (currentFilter === 'completed') {
    filteredTasks = tasks.filter(task => task.completed);
  }

  // Render filteredTasks...
}
```

**Rationale**:
- Simple, readable code
- No additional libraries needed
- Efficient for 1000+ tasks
- Easy to understand for beginners

---

## Decision 9: Error Handling Strategy

**Decision**: Graceful degradation with user-friendly error messages

**Error Scenarios to Handle**:

1. **localStorage Unavailable**:
   - Check: `typeof Storage !== 'undefined'`
   - Fallback: Display warning, app works in-memory only

2. **localStorage Quota Exceeded**:
   - Catch: `try/catch` around `setItem()`
   - Fallback: Alert user, prevent new tasks

3. **JSON Parse Errors**:
   - Catch: Invalid data in localStorage
   - Fallback: Start with empty task list

4. **Empty Task Submission**:
   - Validate: Check for empty/whitespace before adding
   - Feedback: Do nothing or show subtle indication

**Best Practice Pattern**:
```javascript
function saveTasks() {
  try {
    localStorage.setItem('todoAppTasks', JSON.stringify(tasks));
  } catch (error) {
    if (error.name === 'QuotaExceededError') {
      alert('Storage limit reached. Please delete some tasks.');
    }
  }
}
```

---

## Decision 10: Testing Strategy

**Decision**: Manual testing initially, with clear test scenarios

**Testing Approach**:
1. **Manual Testing**: Use quickstart.md as test script
2. **Browser Testing**: Test on Chrome, Firefox, Safari, Edge
3. **Accessibility Testing**:
   - Keyboard-only navigation
   - Screen reader testing (NVDA, JAWS, or VoiceOver)
   - Color contrast validation tools

**Future Consideration**:
- Jest for unit tests (testing pure functions)
- Playwright/Cypress for integration tests
- Only add if complexity increases

**Test Scenarios** (from spec.md):
- Create task, mark complete, delete
- Filter switching
- Browser close/reopen persistence
- Edge cases (empty input, long text, special characters)

---

## Technology Summary

| Decision Area | Technology | Rationale |
|--------------|------------|-----------|
| Language | Vanilla JavaScript ES6+ | Simplicity, no dependencies |
| Storage | localStorage API | Built-in, offline-capable |
| Structure | Single HTML file | Maximum simplicity |
| HTML | Semantic HTML5 | Accessibility, clarity |
| CSS | Inline `<style>` tag | Self-contained, organized |
| Testing | Manual + Browser tools | Appropriate for scope |
| Deployment | Static file hosting | Simple, free options |

---

## Next Steps

With research complete, proceed to:
1. **Phase 1**: Create data-model.md (detailed entity definitions)
2. **Phase 1**: Create quickstart.md (setup and testing guide)
3. **Phase 1**: Generate contracts/ (API interface - not applicable for client-only, but document data flow)
4. **Phase 2**: Generate tasks.md (implementation task list)

All decisions documented here support the constitutional principles and meet the feature requirements specified in spec.md.
