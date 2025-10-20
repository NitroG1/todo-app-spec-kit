# Todo List Application

A simple, accessible, and feature-rich todo list application built with vanilla JavaScript. No frameworks, no dependencies - just clean, beginner-friendly code.

## Features

### Core Functionality
- ✅ **Add, edit, and delete todos** - Manage your tasks easily
- ✅ **Mark todos as complete** - Track your progress
- ✅ **Filter todos** - View all, active, or completed tasks
- ✅ **Persistent storage** - Data saved in browser localStorage

### Due Date Management (Feature 002)
- 📅 **Optional due dates** - Add due dates to any todo
- ✏️ **Inline editing** - Click the edit icon to set or change dates
- 🎨 **Visual indicators** - Color-coded backgrounds and icons for:
  - **Overdue tasks**: Red background + ⚠️ warning icon
  - **Tasks due today**: Orange background + 📅 calendar icon
  - **Future tasks**: Normal appearance
- 🔍 **Smart sorting** - Sort by due date (earliest or latest first)
- 🎯 **Flexible filtering** - Filter by:
  - All tasks
  - Overdue only
  - Due today only
  - Due this week (next 7 days)
  - No due date
- 💾 **Session persistence** - Sort and filter preferences remembered during your session

### Accessibility Features
- ♿ **Keyboard navigation** - Full keyboard support throughout the app
- 🔊 **Screen reader support** - ARIA labels and live region announcements
- 🎨 **WCAG AA compliant** - Dual-coded indicators (color + icons)
- 🎯 **Focus management** - Clear focus indicators for all interactive elements

## Technology Stack

- **HTML5** - Semantic markup
- **CSS3** - Modern styling with flexbox
- **Vanilla JavaScript (ES6+)** - No frameworks or libraries
- **localStorage API** - Client-side data persistence
- **sessionStorage API** - Session-based preference persistence

## Getting Started

### Installation

No installation required! Simply open `index.html` in a modern web browser.

### Browser Support

Works in all modern browsers that support:
- ES6+ JavaScript features (const/let, arrow functions, template literals)
- localStorage API
- HTML5 date input (`<input type="date">`)

**Supported browsers:**
- Chrome 49+
- Firefox 44+
- Safari 10+
- Edge 14+

### Usage

1. **Add a todo**: Type in the input field and click "Add" or press Enter
2. **Set a due date**: Click the ✏️ edit icon next to any todo
3. **Complete a todo**: Click the checkbox
4. **Delete a todo**: Click the "Delete" button
5. **Filter todos**: Use the filter buttons above the list
6. **Sort by due date**: Use the "Sort by" dropdown in the toolbar
7. **Filter by due date**: Use the due date filter buttons in the toolbar

## File Structure

```
todo-app/
├── index.html           # Main application file (HTML + CSS + JavaScript)
├── README.md            # This file
└── specs/              # Feature specifications and planning docs
    ├── 001-build-a-todo/
    └── 002-add-due-dates/
```

## Data Model

### Todo Object Structure

```javascript
{
  id: 1697289600000,          // Unique timestamp-based ID
  text: "Buy groceries",      // Todo description
  completed: false,            // Completion status
  dueDate: "2025-10-20"       // ISO 8601 date (YYYY-MM-DD) or null
}
```

### Storage

- **Todos**: Stored in `localStorage` under key `todoAppTasks`
- **Completion filter**: Stored in `localStorage` under key `todoAppFilter`
- **Sort preference**: Stored in `sessionStorage` under key `todoAppSort`
- **Due date filter**: Stored in `sessionStorage` under key `todoAppDueDateFilter`

## Development

### Code Style

- **Simple and beginner-friendly** - Clear, well-commented code
- **ES6+ features only** - const/let, arrow functions, template literals, .map()/.filter()/.sort()
- **No complex patterns** - Avoiding generators, proxies, destructuring, .reduce()
- **Comprehensive comments** - Every function documented with purpose, parameters, and return values

### Constitutional Principles

This project follows five core principles:

1. **Simplicity** - Code should be easy to understand for beginners
2. **Documentation** - Comprehensive inline comments explain all logic
3. **Modern JavaScript** - ES6+ features with controlled complexity
4. **Accessibility** - WCAG AA compliance with keyboard and screen reader support
5. **Minimal Design** - Zero external dependencies, clean single-file structure

## Accessibility

### Keyboard Navigation

- **Tab** - Navigate between interactive elements
- **Enter/Space** - Activate buttons and checkboxes
- **Arrow keys** - Navigate within dropdowns

### Screen Reader Support

- **ARIA labels** - All icons and buttons have descriptive labels
- **Live region** - Announces todo additions, deletions, and due date changes
- **Semantic HTML** - Proper heading structure and list markup

### Visual Accessibility

- **Color + Icon indicators** - Never relying on color alone
- **Focus indicators** - Clear 3px blue outline on focused elements
- **Color contrast** - WCAG AA compliant color combinations

## Testing

### Manual Testing Checklist

**Basic Functionality:**
- [ ] Add a new todo
- [ ] Mark todo as complete
- [ ] Delete a todo
- [ ] Filter by all/active/completed
- [ ] Data persists after page refresh

**Due Date Features:**
- [ ] Set due date on new todo via edit icon
- [ ] Change existing due date
- [ ] Remove due date (Clear button)
- [ ] Overdue tasks show red background + ⚠️ icon
- [ ] Tasks due today show orange background + 📅 icon
- [ ] Sort by due date (earliest/latest first)
- [ ] Filter by overdue/today/week/no date
- [ ] Sort and filter preferences persist during session

**Accessibility:**
- [ ] Tab through all interactive elements
- [ ] Activate buttons with Enter/Space
- [ ] Focus indicators visible
- [ ] Test with screen reader (NVDA/JAWS/VoiceOver)

### Edge Cases

- [ ] Far-future dates (e.g., 2099-12-31) accepted and displayed
- [ ] Far-past dates (e.g., 2020-01-01) marked as overdue
- [ ] Midnight transition handled correctly (refresh at midnight)
- [ ] ISO 8601 format survives localStorage round-trip
- [ ] Date display respects browser locale
- [ ] Completed todo due dates remain visible

## License

This project is created for educational purposes.

## Changelog

### Feature 002: Add Due Dates (2025-10-14)
- Added optional due date field to todos
- Implemented inline date editing with edit icon
- Added visual indicators for overdue and due-today tasks
- Added toolbar with sort and filter controls
- Implemented session persistence for sort/filter preferences
- Full keyboard and screen reader accessibility
- Zero new dependencies maintained

### Feature 001: Build a Todo (Initial)
- Basic todo CRUD operations
- Completion status tracking
- Filter by all/active/completed
- localStorage persistence
- Accessibility features
- Zero dependencies
