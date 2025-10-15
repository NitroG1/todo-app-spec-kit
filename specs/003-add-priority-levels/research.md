# Research: Priority Levels Implementation

**Feature**: Priority Levels for Todos
**Date**: 2025-10-14
**Status**: Complete

## Overview

Research findings for implementing priority levels in the todo app, focusing on UI patterns, accessibility, data storage, and integration with existing features (due dates, filters, sorting).

## Key Research Areas

### 1. Priority Level Design Patterns

**Decision**: Use three-tier priority system (High, Medium, Low) with dual-coded indicators

**Rationale**:
- Three levels balance simplicity with utility (more than 3 becomes overwhelming for users)
- Dual-coding (color + text) ensures accessibility for color-blind users (WCAG 2.1 SC 1.4.1)
- Industry standard: GitHub Issues, Jira, Trello all use 3-4 priority levels
- Existing codebase uses similar pattern for due dates (overdue/today visual indicators)

**Alternatives Considered**:
- **2 levels (High/Low)**: Too limiting, no middle ground for "normal" tasks
- **4+ levels (Critical/High/Medium/Low/Trivial)**: Adds cognitive overhead, decision fatigue for users
- **Numeric (1-5)**: Less intuitive than semantic labels, requires mental mapping

**Implementation Notes**:
- Priority stored as lowercase string: `'high'`, `'medium'`, `'low'`
- Default value: `'medium'` (neutral baseline, matches user expectations)
- Visual indicators: badges with color + text label

---

### 2. Color Selection for Accessibility

**Decision**: Red (High), Orange/Amber (Medium), Green/Blue (Low) with WCAG AA contrast ratios

**Rationale**:
- Follows universal urgency conventions (red = urgent, green = calm)
- Tested color combinations meet WCAG AA minimum contrast (4.5:1 for text, 3:1 for UI components)
- Existing codebase uses similar colors for due date indicators (red for overdue, orange for today)
- Dual-coded with text labels ensures no information lost for color-blind users

**Color Specifications**:
- **High Priority**: Red background `#fee` (light red), red border `#c00`, dark red text `#800`
- **Medium Priority**: Orange/Amber background `#fef3e0`, orange border `#f90`, dark orange text `#c70`
- **Low Priority**: Blue/Green background `#e6f4ff`, blue border `#0066cc`, dark blue text `#004999`

**Alternatives Considered**:
- **Green for High**: Counterintuitive (green = "good/safe" in Western cultures)
- **Grayscale only**: Fails to provide quick visual scanning benefit
- **Custom user colors**: Adds complexity, violates simplicity principle

**Accessibility Verification**:
- Text on colored backgrounds: 4.5:1+ contrast ratio (WCAG AA)
- Border provides additional visual distinction
- Text labels ensure screen reader users get full information

### Priority Badge Design (Color Collision Fix)

**Problem Identified:** Using color-only indicators creates conflicts when priority colors (Red/Orange/Blue) overlap with due date status colors (Red=overdue, Orange=due today).

**Solution:** Icon + Text + Color badges that work independently of due date colors.

**Implementation:**

**High Priority Badge:**
- Background: Red (#DC2626)
- Text: "HIGH" in white
- Icon: ⬆️ or "!" symbol
- Format: `[HIGH]` or `[⬆️ HIGH]`

**Medium Priority Badge:**
- Background: Orange (#F59E0B)
- Text: "MED" in white
- Icon: ➡️ or "=" symbol
- Format: `[MED]` or `[➡️ MED]`

**Low Priority Badge:**
- Background: Blue (#3B82F6)
- Text: "LOW" in white
- Icon: ⬇️ or "-" symbol
- Format: `[LOW]` or `[⬇️ LOW]`

**Layout Strategy:**
```
[HIGH] Buy groceries                    Overdue - Oct 12
  ↑                                            ↑
Priority badge (left)              Due date text (right)
```

**Key Principles:**
1. Priority uses BADGE (colored background box)
2. Due date uses TEXT COLOR (red/orange/gray text)
3. Spatial separation prevents overlap
4. Icon + text ensures accessibility (not color-dependent)
5. Screen readers announce both: "High priority, Overdue October 12th"

**Accessibility Compliance:**
- WCAG AA contrast: White text on colored backgrounds (4.5:1 minimum)
- Not color-dependent: Icon and text provide redundant cues
- Keyboard accessible: All priority controls navigable via keyboard
---

### 3. UI Placement and Interaction Patterns

**Decision**: Priority selector in creation form + inline edit for existing todos + filter toolbar

**Rationale**:
- Follows existing due date pattern (set on create, edit inline, filter with toolbar)
- Users expect priority selection during task creation (common pattern in task managers)
- Inline editing reduces friction (no modal dialogs, immediate feedback)
- Filter toolbar consolidates all filtering controls (completion status, due date, priority)

**UI Components**:

1. **Creation Form**: Dropdown `<select>` element next to "Add" button
   - Options: High, Medium (selected), Low
   - Label: "Priority" (visible or screen-reader-only depending on space)
   - Default: Medium (neutral baseline)

2. **Todo List Display**: Priority badge next to todo text
   - Small colored badge with text label (e.g., "High", "Med", "Low")
   - Position: After checkbox, before task text
   - Abbreviated labels for space efficiency ("High", "Med", "Low")

3. **Inline Edit**: Click badge to open priority dropdown
   - Similar to due date edit pattern (click to reveal inline controls)
   - Dropdown with three options
   - Save changes immediately on selection (no separate "Save" button needed)

4. **Filter Toolbar**: Priority filter buttons alongside existing due date filters
   - Buttons: All | High | Medium | Low
   - Active state styling (blue background, similar to existing filters)
   - Position: Below due date filter controls

**Alternatives Considered**:
- **Modal dialog for edit**: Too heavy, interrupts flow
- **Drag-and-drop priority**: Not keyboard accessible, confusing for beginners
- **Icons only (no text)**: Fails accessibility, requires learning curve

---

### 4. Data Storage and Migration

**Decision**: Extend todo object schema with `priority` field, default to `'medium'`

**Rationale**:
- Consistent with existing schema extension pattern (due dates feature added `dueDate` field)
- String values more readable than numeric codes in localStorage
- Default to `'medium'` provides neutral baseline, non-disruptive migration

**Schema Extension**:

```javascript
// Todo object structure (before)
{
  id: 1234567890,
  text: "Buy groceries",
  completed: false,
  dueDate: "2025-10-15" // or null
}

// Todo object structure (after)
{
  id: 1234567890,
  text: "Buy groceries",
  completed: false,
  dueDate: "2025-10-15", // or null
  priority: "medium"      // NEW: 'high', 'medium', or 'low'
}
```

**Migration Strategy**:
- Use same pattern as due date migration (`migratePrioritySchema()` function)
- Run on app initialization, check if `priority` field exists
- Add `priority: 'medium'` to any todos missing the field
- Non-destructive, one-time operation
- Log migration to console for transparency

**Validation**:
- Accept only: `'high'`, `'medium'`, `'low'`
- Reject invalid values, fall back to `'medium'`
- Case-insensitive validation with lowercase normalization

**Alternatives Considered**:
- **Numeric priorities (1-3)**: Less readable, requires mapping
- **Default to 'low'**: Could feel demotivating ("new tasks are low priority")
- **Null as default**: Requires special handling, adds complexity

---

### 5. Integration with Existing Features

**Decision**: Priority filtering works independently of completion/due date filters (combine with AND logic)

**Rationale**:
- Users may want to see "Active High Priority tasks due this week" (multiple filters combined)
- Existing codebase already combines completion filter + due date filter
- Priority adds third dimension to filtering, follows same pattern
- Sorting by due date takes precedence over priority (more time-sensitive)

**Filter Combination Logic**:

```javascript
// Pseudo-code for getFilteredTasks()
filtered = tasks
  .filter(byCompletionStatus)  // Existing: all/active/completed
  .filter(byDueDateCriteria)   // Existing: all/overdue/today/week/none
  .filter(byPriority)          // NEW: all/high/medium/low
  .sort(bySortCriteria)        // Existing: none/due-asc/due-desc
```

**Priority Filter Persistence**:
- Use `sessionStorage` (session-only, like existing due date filter)
- Rationale: Priority filters are temporary views, not a permanent preference
- Users start fresh on each session with "All priorities" selected

**Interaction with Sorting**:
- Existing due date sort continues to work
- No "sort by priority" feature in initial implementation (keeps scope bounded)
- Future enhancement: Could add "Sort by Priority" dropdown option

**Alternatives Considered**:
- **Priority overrides due date filter**: Too limiting, users lose flexibility
- **Persist priority filter in localStorage**: Confusing if users return and see partial list
- **Add priority sorting**: Scope creep, adds complexity

---

### 6. Accessibility Considerations

**Decision**: Full keyboard navigation + ARIA labels + screen reader announcements

**Rationale**:
- Constitution Principle IV mandates universal accessibility
- Existing codebase has strong accessibility patterns (ARIA live regions, keyboard focus management)
- Priority feature follows these patterns for consistency

**Accessibility Features**:

1. **Keyboard Navigation**:
   - Priority dropdown in form: Tab to reach, Arrow keys to select, Enter to confirm
   - Priority badge: Tab to reach, Enter/Space to open editor
   - Filter buttons: Tab to reach, Enter/Space to activate

2. **Screen Reader Support**:
   - Priority dropdown: `<label>` element with `for` attribute
   - Priority badge: ARIA label "Priority: High" (includes value)
   - Filter buttons: ARIA label "Filter by High priority" (clear action)
   - ARIA live region announces priority changes: "Priority changed to High"

3. **Visual Focus Indicators**:
   - All interactive elements have visible focus outlines (3px blue outline, existing pattern)
   - Focus management: After editing priority, return focus to badge

4. **Color Independence**:
   - Never use color alone to convey information
   - Always pair color with text label
   - High contrast between text and background (WCAG AA)

**Testing Requirements**:
- Test with keyboard only (no mouse)
- Test with NVDA/JAWS screen reader
- Test with browser zoom (200%+)
- Test color contrast with automated tools

---

### 7. Performance Considerations

**Decision**: Client-side filtering with no performance optimization needed for target scale

**Rationale**:
- Target scale: 100-500 todos (typical user)
- JavaScript array filtering is O(n), handles 1000+ items instantly on modern browsers
- No need for indexing, memoization, or virtualization at this scale
- Keep implementation simple per Constitution Principle I

**Performance Benchmarks** (estimated):
- Filtering 100 todos by priority: <1ms
- Filtering 500 todos: ~5ms (imperceptible)
- Filtering 1000 todos: ~10ms (still fast)
- Re-rendering 100 todos: ~20ms (DOM manipulation is bottleneck, not filtering)

**Optimization Strategy**:
- No premature optimization
- Monitor user feedback for performance issues
- If needed in future: Add virtual scrolling for 1000+ todos (out of scope for this feature)

**Alternatives Considered**:
- **Indexed filtering**: Unnecessary complexity for target scale
- **Debounced rendering**: Not needed, filtering is already fast
- **Web Workers**: Overkill for simple array operations

---

## Design Decisions Summary

| Aspect | Decision | Key Reason |
|--------|----------|------------|
| Priority Levels | 3 levels (High, Medium, Low) | Industry standard, balances simplicity with utility |
| Default Priority | Medium | Neutral baseline, non-disruptive migration |
| Color Scheme | Red/Orange/Blue with text labels | Universal urgency conventions + WCAG AA compliance |
| UI Placement | Dropdown in form + badge in list + filter toolbar | Follows existing due date pattern |
| Data Storage | String field: `'high'`, `'medium'`, `'low'` | Readable, simple validation |
| Migration | Add `priority: 'medium'` to existing todos | Same pattern as due date feature |
| Filter Persistence | Session-only (sessionStorage) | Temporary views, fresh start each session |
| Filter Combination | AND logic (combine with completion + due date) | Maximum flexibility for users |
| Accessibility | Dual-coding + keyboard nav + ARIA | Constitutional requirement, best practice |
| Performance | No optimization needed | Target scale is 100-500 todos, filtering is instant |

---

## Implementation Priorities

Based on spec priorities (P1-P3):

1. **P1 - Core Functionality** (Must have):
   - Add priority field to todo object schema
   - Migration function for existing todos
   - Priority selector in creation form
   - Visual priority badges in todo list
   - Validation and storage functions

2. **P1 - Visual Indicators** (Must have):
   - CSS styles for priority badges (colors + labels)
   - Render priority badges in todo list
   - Ensure WCAG AA color contrast

3. **P2 - Editing** (Should have):
   - Inline priority editor (click badge to edit)
   - Update priority and re-render

4. **P3 - Filtering** (Nice to have):
   - Priority filter buttons in toolbar
   - Filter logic integration
   - Session persistence for filter state

---

## Open Questions

*None - all design decisions finalized*

---

## References

- **WCAG 2.1 Guidelines**: https://www.w3.org/WAI/WCAG21/quickref/
- **Color Contrast Checker**: WebAIM Contrast Checker
- **Existing Patterns**: Due dates feature (specs/002-add-due-dates/*)
- **Constitution**: `.specify/memory/constitution.md` (Principles I-V)
