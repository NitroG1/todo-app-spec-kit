# Tasks: Add Due Dates to Todos

**Input**: Design documents from `/specs/002-add-due-dates/`
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, quickstart.md, contracts/function-interfaces.md

**Tests**: Manual testing approach - no automated test tasks generated per quickstart.md

**Organization**: Tasks are grouped by user story (P1-P4) to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`
- **[P]**: Can run in parallel (different sections, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3, US4)
- File path: `index.html` (single-file web application)

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and data model foundation

- [x] T001 Read existing `index.html` to understand current todo app structure and code organization
- [x] T002 [P] Add ARIA live region to HTML `<body>` for screen reader announcements (after existing content)
- [x] T003 [P] Add CSS section comments in `<style>` to organize: "Due Date Toolbar", "Due Date Display", "Visual Indicators"

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core data model and migration that ALL user stories depend on

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [x] T004 Extend `createTask()` function in `index.html <script>` to add `dueDate: null` field to returned todo object
- [x] T005 Implement `migrateTasksSchema()` function after `loadTasksFromStorage()` in `index.html <script>` per data-model.md lines 150-176
- [x] T006 Call `migrateTasksSchema()` in `DOMContentLoaded` event listener before `loadAndRenderTasks()` in `index.html <script>`
- [x] T007 [P] Implement `isValidDueDate(dueDateString)` validation function per contracts/function-interfaces.md lines 418-444 in `index.html <script>`
- [x] T008 [P] Implement `getDueDateStatus(dueDateString)` function per contracts/function-interfaces.md lines 67-95 in `index.html <script>`

**Checkpoint**: Data model extended, migration ready, validation functions complete - user story implementation can now begin

---

## Phase 3: User Story 1 - Set Due Date on Todo (Priority: P1) 🎯 MVP

**Goal**: Users can add an optional due date when creating a new todo or edit an existing todo to add/change/remove its due date

**Independent Test**: Create a todo with a due date, edit a todo to add a due date, change a due date, clear a due date, and verify dates are stored/displayed correctly (per spec.md lines 28-34)

### Implementation for User Story 1

- [x] T009 [P] Implement `formatDueDateForDisplay(dueDateString)` function per contracts/function-interfaces.md lines 100-123 in `index.html <script>`
- [x] T010 [P] Implement `announceToScreenReader(message)` utility function per contracts/function-interfaces.md lines 450-471 - use for due date changes (T011/T012), sort changes (T032), and filter changes (T039) to satisfy FR-015 in `index.html <script>`
- [x] T011 Implement `setTodoDueDate(todoId, dueDateString)` function per contracts/function-interfaces.md lines 11-36 in `index.html <script>` (depends on T007, T009, T010)
- [x] T012 Implement `removeTodoDueDate(todoId)` function per contracts/function-interfaces.md lines 40-63 in `index.html <script>` (depends on T010)
- [x] T013 Add edit icon button (✏️) next to each todo item in todo rendering function in `index.html <script>` per quickstart.md lines 278-318
- [x] T014 Add inline date picker container (hidden by default) with `<input type="date">`, Save, and Clear buttons per quickstart.md lines 302-308 in todo item HTML
- [x] T015 Implement `toggleDatePicker(todoId)` function (show/hide picker, manage focus) per quickstart.md lines 320-340 in `index.html <script>`
- [x] T016 Implement `handleDueDateChange(event, todoId)` function per contracts/function-interfaces.md lines 300-323 in `index.html <script>` (depends on T011, T012, T015)
- [x] T017 Add CSS for edit icon button (`.edit-btn`) - positioning, sizing, hover states in `index.html <style>`
- [x] T018 Add CSS for inline date picker container (`.date-picker-container`) - inline layout, button styles per quickstart.md lines 113-120 in `index.html <style>`
- [x] T019 Display formatted due date next to todo text in todo rendering function (if `dueDate` exists) using `formatDueDateForDisplay()` in `index.html <script>`
- [x] T020 Add CSS for due date display (`.due-date`) - font size, color, spacing in `index.html <style>`

**Checkpoint**: At this point, User Story 1 (P1) should be fully functional - users can set, edit, and remove due dates with inline editing, and dates persist across refreshes

**Manual Test (per quickstart.md Phase 4 Test 4.1)**:
1. Create a new todo
2. Click edit icon → verify date picker appears and receives focus
3. Select a date → verify it's saved and displayed
4. Click edit icon again to edit → change date → verify update
5. Click Clear → verify due date is removed
6. Refresh page → verify due date persistence

---

## Phase 4: User Story 2 - Display Due Dates with Todos (Priority: P2)

**Goal**: Users can see due dates displayed clearly with each todo, with visual indicators (color + icon) for overdue, due today, and future tasks

**Independent Test**: Create todos with various due dates (past, today, future, none) and verify each displays its due date with appropriate visual styling and indicators (per spec.md lines 44-52)

### Implementation for User Story 2

- [x] T021 [P] Implement `getDueDateIconAndLabel(status)` function per contracts/function-interfaces.md lines 127-151 in `index.html <script>`
- [x] T022 Modify todo rendering function to calculate status using `getDueDateStatus()` and apply status CSS class and icon per quickstart.md lines 392-401 in `index.html <script>` (depends on T008, T021)
- [x] T023 Add CSS for `.todo-item.overdue` - background color (#fee), red border-left (3px solid #c00) per quickstart.md lines 367-370 and research.md lines 86-99 in `index.html <style>`
- [x] T024 Add CSS for `.todo-item.due-today` - background color (#ffedd5), orange border-left (3px solid #f90) per quickstart.md lines 372-375 in `index.html <style>`
- [x] T025 Add CSS for `.status-icon` - margin, font size per quickstart.md lines 377-380 in `index.html <style>`
- [x] T026 Update completed todo styling to show due date indicators at 50% opacity per spec.md lines 132-135 in `index.html <style>`

**Checkpoint**: At this point, User Stories 1 AND 2 should both work - users can set due dates (US1) and see visual indicators for overdue/today/future status (US2)

**Manual Test (per quickstart.md Phase 5 Test 5.1)**:
1. Create todo with yesterday's date → verify red background + ⚠️ icon
2. Create todo with today's date → verify orange background + 📅 icon
3. Create todo with future date → verify normal appearance
4. Complete a todo with due date → verify indicators at 50% opacity
5. Verify ARIA labels present on status icons for screen readers

---

## Phase 5: User Story 3 - Sort Todos by Due Date (Priority: P3)

**Goal**: Users can sort their todo list by due date (earliest first or latest first) with appropriate handling of todos without due dates

**Independent Test**: Create multiple todos with different due dates and verify sort controls arrange them correctly (ascending/descending) with undated todos at the end (per spec.md lines 62-69)

### Implementation for User Story 3

- [x] T027 Add toolbar HTML `<div class="toolbar">` above `<ul id="todo-list">` with sort controls per quickstart.md lines 172-200 in `index.html`
- [x] T028 Add CSS for `.toolbar` - flexbox layout, padding, background, border, spacing per quickstart.md lines 212-221 in `index.html <style>`
- [x] T029 Add CSS for `.sort-controls` and `#sort-select` - layout, styling per quickstart.md lines 223-234 in `index.html <style>`
- [x] T030 [P] Implement `compareDueDates(todoA, todoB)` function per contracts/function-interfaces.md lines 186-209 in `index.html <script>`
- [x] T031 Implement `sortTodosByDueDate(todos, direction)` function per contracts/function-interfaces.md lines 156-183 in `index.html <script>` (depends on T030)
- [x] T032 Implement `handleSortChange(event)` function per contracts/function-interfaces.md lines 327-350 in `index.html <script>` (depends on T031) - call `announceToScreenReader()` to announce sort changes per FR-015
- [x] T033 Add event listener for `#sort-select` change event calling `handleSortChange()` per quickstart.md lines 427-439 in `index.html <script>`
- [x] T034 Create `renderTasksArray(tasks)` helper function to render a given array of todos without modifying localStorage in `index.html <script>`
- [x] T034a [P] Implement session persistence for sort/filter state: store current sort selection and active filter in sessionStorage on change, restore on page load in `DOMContentLoaded` handler in `index.html <script>` (satisfies FR-012)

**Checkpoint**: At this point, User Stories 1, 2, AND 3 should all work - users can set due dates (US1), see visual indicators (US2), and sort by due date (US3)

**Manual Test (per quickstart.md Phase 6 Test 6.1)**:
1. Create 3 todos with different due dates (e.g., Oct 10, Oct 20, Oct 15)
2. Select "Due Date (Earliest First)" → verify order: Oct 10, 15, 20
3. Select "Due Date (Latest First)" → verify order: Oct 20, 15, 10
4. Verify todos without due dates appear at the end in both sort orders
5. Add new todo while sorted → verify it appears in correct position

---

## Phase 6: User Story 4 - Filter Todos by Due Date (Priority: P4)

**Goal**: Users can filter their todo list to show only todos matching specific due date criteria (overdue, due today, due this week, no due date)

**Independent Test**: Create todos across various due date ranges and verify each filter option shows only the appropriate todos (per spec.md lines 78-88)

### Implementation for User Story 4

- [x] T035 Add filter controls to toolbar (inside `.toolbar`) with filter buttons per quickstart.md lines 192-199 in `index.html`
- [x] T036 Add CSS for `.filter-controls`, `.filter-btn`, and `.filter-btn.active` states per quickstart.md lines 236-260 in `index.html <style>`
- [x] T037 [P] Implement `isWithinNextWeek(dueDateString)` function per contracts/function-interfaces.md lines 244-269 in `index.html <script>`
- [x] T038 Implement `filterTodosByDueDateCriteria(todos, criteria)` function per contracts/function-interfaces.md lines 214-242 in `index.html <script>` (depends on T008, T037)
- [x] T039 Implement `handleFilterClick(event)` function per contracts/function-interfaces.md lines 355-377 in `index.html <script>` (depends on T038) - call `announceToScreenReader()` to announce filter changes per FR-015
- [x] T040 Add event listeners to all `.filter-btn` elements calling `handleFilterClick()` per quickstart.md lines 451-467 in `index.html <script>`

**Checkpoint**: All user stories (P1-P4) should now be independently functional - complete due date feature with set/display/sort/filter capabilities

**Manual Test (per quickstart.md Phase 6 Test 6.2)**:
1. Create todos: overdue, due today, due next week, due next month, no date
2. Click "Overdue" → only overdue todos show
3. Click "Due Today" → only today's todos show
4. Click "This Week" → todos due within 7 days show
5. Click "No Due Date" → only undated todos show
6. Click "All" → all todos show again
7. Test filter + sort combinations work together

---

## Phase 7: Polish & Cross-Cutting Concerns

**Purpose**: Accessibility, validation, documentation, and constitution compliance

- [x] T041 Add comprehensive function comments (purpose, params, returns) to all 17 functions per Principle II in `index.html <script>`
- [x] T042 [P] Add inline code comments explaining ISO 8601 usage, date comparison logic, and migration strategy per plan.md lines 55-60 in `index.html <script>`
- [x] T043 [P] Verify all keyboard navigation works: Tab through toolbar → todos → edit icons, Enter/Space activate controls, date picker keyboard accessible per quickstart.md Phase 7 Test 7.1
- [x] T044 [P] Verify ARIA labels present on: edit icons, status icons, toolbar controls, and ARIA live region announces changes per quickstart.md Phase 7 Test 7.2
- [x] T045 Test with screen reader (NVDA/JAWS/VoiceOver) - verify all interactions announced correctly per quickstart.md lines 515-521
- [x] T046 [P] Verify color contrast ratios meet WCAG AA standards for overdue/today indicators per research.md lines 86-99
- [x] T047 Run end-to-end validation per quickstart.md Phase 8 Final Validation lines 563-577 (all 12 test scenarios) PLUS explicit edge case testing per spec.md lines 94-100: (1) far-future dates accepted/displayed, (2) far-past dates accepted/marked overdue, (3) midnight transition handling (verify via refresh), (4) ISO 8601 format survives localStorage round-trip, (5) date display respects locale, (6) completed todo due dates remain visible
- [x] T048 [P] Update README.md with due date feature description, usage instructions, and accessibility notes
- [x] T049 Verify constitution compliance checklist complete per quickstart.md Phase 8 lines 533-559 (all 5 principles)

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Story 1/P1 (Phase 3)**: Depends on Foundational (Phase 2) - MVP foundation
- **User Story 2/P2 (Phase 4)**: Depends on Foundational (Phase 2) AND User Story 1 (needs due date data to display)
- **User Story 3/P3 (Phase 5)**: Depends on Foundational (Phase 2) AND User Story 1 (needs due date data to sort)
- **User Story 4/P4 (Phase 6)**: Depends on Foundational (Phase 2) AND User Story 1 (needs due date data to filter)
- **Polish (Phase 7)**: Depends on all desired user stories being complete

### User Story Dependencies

```
Foundation (T004-T008) ─┬─→ US1/P1 (T009-T020) ─┬─→ US2/P2 (T021-T026)
                        │                        ├─→ US3/P3 (T027-T034)
                        │                        └─→ US4/P4 (T035-T040)
                        └─────────────────────────→ All depend on Foundation

US1 is foundational for US2, US3, US4 (they need due date data to operate on)
US2, US3, US4 can proceed in parallel after US1 is complete
```

### Within Each User Story

**US1 (Set Due Date)**:
- T007, T008 (validation/status) must complete before T011 (setTodoDueDate)
- T009, T010 (formatting/announce) must complete before T011 (setTodoDueDate)
- T011, T012, T015 (set/remove/toggle) must complete before T016 (handleDueDateChange)
- T013-T014 (HTML) can run in parallel with functions T009-T012
- T017-T018 (CSS) can run in parallel after T013-T014 (HTML)
- T019 (rendering) depends on T009 (formatDueDateForDisplay)

**US2 (Display Indicators)**:
- T021 (icon function) can run in parallel with CSS tasks T023-T025
- T022 (apply status to rendering) depends on T008 (getDueDateStatus) and T021 (getDueDateIconAndLabel)
- T023-T026 (CSS) can all run in parallel

**US3 (Sort)**:
- T027-T029 (toolbar HTML/CSS) can run in parallel
- T030 (compareDueDates) before T031 (sortTodosByDueDate)
- T031 (sortTodosByDueDate) before T032 (handleSortChange)
- T034 (renderTasksArray helper) can run anytime after T027 (toolbar exists)

**US4 (Filter)**:
- T035-T036 (filter UI) can run in parallel
- T037 (isWithinNextWeek) before T038 (filterTodosByDueDateCriteria)
- T038 (filter function) before T039 (handleFilterClick)

### Parallel Opportunities

**Phase 1 (Setup)**: T002 and T003 can run in parallel (different sections of HTML)

**Phase 2 (Foundational)**: T007 and T008 can run in parallel (independent validation functions)

**Phase 3 (US1)**:
- T009 and T010 can run in parallel (independent utility functions)
- T013-T014 (HTML) can run in parallel with T009-T012 (functions)
- T017-T018 (CSS) can run in parallel

**Phase 4 (US2)**: T021, T023, T024, T025 can all run in parallel (independent CSS and function)

**Phase 5 (US3)**: T027, T028, T029 can run in parallel (HTML and CSS)

**Phase 6 (US4)**: T035, T036, T037 can run in parallel (UI and function)

**Phase 7 (Polish)**: T041, T042, T043, T044, T046, T048 can run in parallel (different concerns)

---

## Parallel Example: User Story 1 (Set Due Date)

```bash
# Launch utility functions together:
Task T009: "Implement formatDueDateForDisplay() function"
Task T010: "Implement announceDueDateChange() function"

# Launch HTML structure tasks together (after functions):
Task T013: "Add edit icon button to each todo item"
Task T014: "Add inline date picker container"

# Launch CSS tasks together:
Task T017: "Add CSS for edit icon button"
Task T018: "Add CSS for inline date picker container"
Task T020: "Add CSS for due date display"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

**Recommended for fastest value delivery:**

1. Complete Phase 1: Setup (T001-T003) → ~15 min
2. Complete Phase 2: Foundational (T004-T008) → ~30 min - CRITICAL for all features
3. Complete Phase 3: User Story 1 (T009-T020) → ~90 min
4. **STOP and VALIDATE**: Test User Story 1 independently per manual tests
5. **Deploy/Demo MVP**: Users can now set, edit, and remove due dates!

**Total MVP Time**: ~2.5 hours for basic due date functionality

### Incremental Delivery (Build on MVP)

**Add features incrementally after MVP:**

1. **MVP (US1)**: Set due dates → Test → Deploy ✅
2. **+Visual Indicators (US2)**: Complete Phase 4 (T021-T026) → ~30 min → Test → Deploy ✅
3. **+Sorting (US3)**: Complete Phase 5 (T027-T034) → ~45 min → Test → Deploy ✅
4. **+Filtering (US4)**: Complete Phase 6 (T035-T040) → ~30 min → Test → Deploy ✅
5. **+Polish (Phase 7)**: Complete T041-T049 → ~60 min → Final validation ✅

**Total Full Feature Time**: ~5 hours for complete implementation

### Parallel Team Strategy

With multiple developers (after Foundation is complete):

**Single Developer Sequential** (recommended for this feature):
- Foundation → US1 → US2 → US3 → US4 → Polish
- Reason: Single file (index.html) - parallel work would cause merge conflicts

**Possible Parallel Approach** (if needed):
1. Developer A: Complete Foundation (T004-T008) - BLOCKS everything
2. Developer A: Complete US1 (T009-T020) - BLOCKS US2/US3/US4
3. After US1 complete, developers can split:
   - Developer A: US2 visual indicators (T021-T026)
   - Developer B: US3 sorting functions (T030-T034, postpone UI until merge)
   - Developer C: US4 filtering functions (T037-T039, postpone UI until merge)
4. Merge and integrate toolbar UI (T027-T029, T035-T036)

**Note**: Single-file structure makes parallel work challenging. Sequential execution recommended.

---

## Task Summary

**Total Tasks**: 50

**By Phase**:
- Phase 1 (Setup): 3 tasks
- Phase 2 (Foundational): 5 tasks ⚠️ CRITICAL PATH
- Phase 3 (US1 - Set Due Date): 12 tasks 🎯 MVP
- Phase 4 (US2 - Display Indicators): 6 tasks
- Phase 5 (US3 - Sort): 9 tasks (includes T034a for FR-012 persistence)
- Phase 6 (US4 - Filter): 6 tasks
- Phase 7 (Polish): 9 tasks

**By User Story**:
- US1/P1 (Set Due Date): 12 tasks - MVP FOUNDATION
- US2/P2 (Display Indicators): 6 tasks - VISUAL FEEDBACK
- US3/P3 (Sort): 8 tasks - ORGANIZATION
- US4/P4 (Filter): 6 tasks - FOCUS

**Parallel Opportunities**: 18 tasks marked [P] can run in parallel within their phase

**File Locations**: All tasks modify `index.html` (HTML, CSS, JavaScript sections)

---

## Notes

- Single-file architecture (index.html) means most tasks touch the same file - careful sequencing required
- Foundation (Phase 2) is CRITICAL PATH - blocks all user stories
- User Story 1 (P1) is MVP and also blocks US2/US3/US4 (they need due date data)
- Each user story has clear test criteria in "Independent Test" section
- Manual testing approach per quickstart.md - no automated test tasks
- Constitution compliance verified throughout (comments, accessibility, simplicity)
- All functions documented per Principle II (comprehensive comments)
- Zero dependencies maintained (native HTML date input, no libraries)
- Accessibility first: dual-coded indicators, ARIA labels, keyboard navigation, screen reader support
