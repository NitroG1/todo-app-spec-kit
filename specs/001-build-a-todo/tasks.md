# Tasks: Todo List Application

**Input**: Design documents from `/specs/001-build-a-todo/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Manual testing approach per quickstart.md. No automated tests in this implementation.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions
- **Single file structure**: All code in `index.html` at repository root
- Paths shown below assume single-file approach per plan.md

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic HTML structure

- [ ] T001 Create `index.html` file in repository root with basic DOCTYPE and HTML structure
- [ ] T002 Add viewport meta tag and UTF-8 charset to `<head>` section in index.html
- [ ] T003 Add page title "Todo List Application" to `<head>` section in index.html

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core HTML structure and CSS that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T004 Create semantic HTML structure in `<body>` of index.html: `<main>`, heading, task form, filter buttons container, task list `<ul>`, and ARIA live region
- [ ] T005 Add `<style>` tag in `<head>` with CSS reset (box-sizing, margins, body defaults) in index.html
- [ ] T006 Add layout CSS in `<style>` tag (container, spacing, responsive width) in index.html
- [ ] T007 Add form and input CSS in `<style>` tag (input field, add button, labels) in index.html
- [ ] T008 Add filter buttons CSS in `<style>` tag (button group, active state, hover) in index.html
- [ ] T009 Add task list CSS in `<style>` tag (list reset, task item layout, checkbox, delete button) in index.html
- [ ] T010 Add completed task CSS in `<style>` tag (strikethrough, opacity/color changes) in index.html
- [ ] T011 Add accessibility CSS in `<style>` tag (focus indicators, skip links, WCAG AA contrast) in index.html
- [ ] T012 Add `<script>` tag before closing `</body>` with state variables (tasks array, currentFilter) in index.html
- [ ] T013 [P] Add utility function `generateTaskId()` in `<script>` tag in index.html with comment block
- [ ] T014 [P] Add utility function `isValidTaskText(text)` in `<script>` tag in index.html with comment block
- [ ] T015 [P] Add utility function `escapeHTML(text)` in `<script>` tag in index.html with comment block

**Checkpoint**: Foundation ready - HTML structure complete, CSS applied, basic JS utilities in place. User story implementation can now begin.

---

## Phase 3: User Story 1 - Create and View Tasks (Priority: P1) 🎯 MVP

**Goal**: Users can add tasks by typing text and submitting, and see them displayed in a list

**Independent Test**: Open app, type "Buy groceries", submit, verify task appears in list with unchecked checkbox

### Implementation for User Story 1

- [ ] T016 [US1] Add storage function `saveTasks()` in `<script>` tag in index.html with try/catch for QuotaExceededError and comment block
- [ ] T017 [US1] Add storage function `loadTasks()` in `<script>` tag in index.html with JSON parse error handling and comment block
- [ ] T018 [US1] Add rendering function `renderTasks()` in `<script>` tag in index.html that clears task list, gets tasks array, creates `<li>` elements with template literals, and updates DOM
- [ ] T019 [US1] Add task management function `addTask(taskText)` in `<script>` tag in index.html that validates, creates task object, pushes to array, calls saveTasks(), calls renderTasks(), with comment block
- [ ] T020 [US1] Add event handler `handleFormSubmit(event)` in `<script>` tag in index.html that prevents default, gets input value, validates, calls addTask(), clears input field, with comment block
- [ ] T021 [US1] Add initialization function `init()` in `<script>` tag in index.html that loads tasks, renders initial UI, sets up form submit event listener, with comment block
- [ ] T022 [US1] Add DOMContentLoaded event listener in `<script>` tag in index.html that calls init()

**Checkpoint**: At this point, User Story 1 should be fully functional - users can add tasks and see them persist in the list (but can't yet mark complete, delete, or filter)

---

## Phase 4: User Story 5 - Persistent Storage (Priority: P1)

**Goal**: Tasks survive browser close/reopen and persist across sessions

**Independent Test**: Add 3 tasks, close browser completely, reopen app, verify all 3 tasks are still there

**Note**: Storage functions were created in US1 phase. This phase adds filter persistence and localStorage availability check.

### Implementation for User Story 5

- [ ] T023 [P] [US5] Add storage function `saveFilter()` in `<script>` tag in index.html with comment block
- [ ] T024 [P] [US5] Add storage function `loadFilter()` in `<script>` tag in index.html with default 'all' fallback and comment block
- [ ] T025 [US5] Add utility function `isLocalStorageAvailable()` in `<script>` tag in index.html with try/catch test and comment block
- [ ] T026 [US5] Update `init()` function in `<script>` tag in index.html to check localStorage availability and show warning if unavailable
- [ ] T027 [US5] Update `init()` function in `<script>` tag in index.html to load filter state from localStorage and apply it

**Checkpoint**: At this point, User Stories 1 AND 5 work together - tasks persist across browser sessions

---

## Phase 5: User Story 2 - Mark Tasks Complete (Priority: P2)

**Goal**: Users can click checkbox to toggle task completion status with visual feedback

**Independent Test**: Pre-populate tasks, click a checkbox, verify strikethrough appears and checkbox is checked; click again to uncheck

### Implementation for User Story 2

- [ ] T028 [US2] Add task management function `toggleTaskComplete(taskId)` in `<script>` tag in index.html that finds task by ID, toggles completed property, calls saveTasks(), calls renderTasks(), with comment block
- [ ] T029 [US2] Add event handler `handleCheckboxChange(taskId)` in `<script>` tag in index.html that calls toggleTaskComplete(), with comment block
- [ ] T030 [US2] Update `renderTasks()` function in `<script>` tag in index.html to add checkbox input with checked state based on task.completed and attach change event listener
- [ ] T031 [US2] Update `renderTasks()` function in `<script>` tag in index.html to add CSS class (e.g., 'completed') to task `<li>` when task.completed is true

**Checkpoint**: User Stories 1, 2, AND 5 now work together - users can create, view, complete, and persist tasks

---

## Phase 6: User Story 3 - Delete Tasks (Priority: P3)

**Goal**: Users can remove tasks from their list by clicking a delete button

**Independent Test**: Pre-populate tasks, click delete on one task, verify it's removed; refresh page to verify deletion persists

### Implementation for User Story 3

- [ ] T032 [US3] Add task management function `deleteTask(taskId)` in `<script>` tag in index.html that finds task index by ID, removes with splice(), calls saveTasks(), calls renderTasks(), with comment block
- [ ] T033 [US3] Add event handler `handleDeleteClick(taskId)` in `<script>` tag in index.html that calls deleteTask(), with comment block
- [ ] T034 [US3] Update `renderTasks()` function in `<script>` tag in index.html to add delete button with aria-label="Delete task" and attach click event listener to each task item

**Checkpoint**: User Stories 1, 2, 3, AND 5 now work - users have full CRUD operations on tasks with persistence

---

## Phase 7: User Story 4 - Filter Tasks by Status (Priority: P4)

**Goal**: Users can filter their view to see all, only active, or only completed tasks

**Independent Test**: Create mix of active and completed tasks, click "Active" filter to see only incomplete tasks, click "Completed" to see only complete tasks, click "All" to see everything

### Implementation for User Story 4

- [ ] T035 [US4] Add filter function `getFilteredTasks()` in `<script>` tag in index.html that returns tasks filtered by currentFilter ('all'/'active'/'completed'), with comment block
- [ ] T036 [US4] Add filter function `setFilter(filterValue)` in `<script>` tag in index.html that updates currentFilter, calls saveFilter(), calls renderTasks(), calls renderFilterButtons(), with comment block
- [ ] T037 [US4] Add rendering function `renderFilterButtons()` in `<script>` tag in index.html that updates active class on filter buttons based on currentFilter, with comment block
- [ ] T038 [US4] Add event handler `handleFilterClick(filterValue)` in `<script>` tag in index.html that calls setFilter(), with comment block
- [ ] T039 [US4] Update `renderTasks()` function in `<script>` tag in index.html to call getFilteredTasks() instead of using tasks array directly
- [ ] T040 [US4] Update `init()` function in `<script>` tag in index.html to set up click event listeners on all three filter buttons (all, active, completed)
- [ ] T041 [US4] Update `init()` function in `<script>` tag in index.html to call renderFilterButtons() for initial filter button state

**Checkpoint**: All user stories now work - complete todo app with create, complete, delete, filter, and persistence

---

## Phase 8: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories and final quality checks

- [ ] T042 [P] Add comprehensive comment blocks to all functions in `<script>` tag in index.html (purpose, parameters, return values, per constitution Principle II)
- [ ] T043 [P] Add inline comments to complex logic blocks in `<script>` tag in index.html (localStorage operations, filtering, rendering) with beginner-friendly explanations
- [ ] T044 [P] Add variable purpose comments in `<script>` tag in index.html for global state and any non-obvious variables
- [ ] T045 Review all function names in `<script>` tag in index.html for clarity and self-documentation per constitution Principle I
- [ ] T046 Test with keyboard-only navigation per quickstart.md (Tab through all controls, Enter/Space to activate, verify focus indicators)
- [ ] T047 Test with screen reader (NVDA/VoiceOver) per quickstart.md and verify ARIA live region announces task additions/deletions
- [ ] T048 Verify color contrast ratios using browser DevTools per quickstart.md (all text must meet WCAG AA 4.5:1 minimum)
- [ ] T049 Test all edge cases from spec.md (empty input, whitespace-only, long text, special characters, no tasks state, storage quota, localStorage disabled)
- [ ] T050 Test browser compatibility per quickstart.md (Chrome, Firefox, Safari, Edge)
- [ ] T051 Create README.md in repository root with project description, setup instructions (just open index.html), features list, accessibility statement, and constitution compliance notes
- [ ] T052 Add inline CSS organization comments in `<style>` tag in index.html (mark sections: reset, layout, components, states, accessibility)

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Story 1 (Phase 3)**: Depends on Foundational phase
- **User Story 5 (Phase 4)**: Can start after US1 complete (builds on storage functions)
- **User Story 2 (Phase 5)**: Can start after US1 complete (extends rendering)
- **User Story 3 (Phase 6)**: Can start after US1 complete (extends rendering)
- **User Story 4 (Phase 7)**: Depends on US1, US2 complete (needs both active and completed tasks to filter)
- **Polish (Phase 8)**: Depends on all user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: No dependencies on other stories - this is the foundation
- **User Story 5 (P1)**: Depends on US1 (extends it with persistence) - should be done immediately after US1
- **User Story 2 (P2)**: Depends on US1 (adds toggle functionality to displayed tasks)
- **User Story 3 (P3)**: Depends on US1 (adds delete functionality to displayed tasks)
- **User Story 4 (P4)**: Depends on US1 and US2 (needs tasks with different completion states to filter)

### Recommended Implementation Order

1. Phase 1: Setup (T001-T003)
2. Phase 2: Foundational (T004-T015) - CRITICAL BLOCKING PHASE
3. Phase 3: User Story 1 - Create/View (T016-T022) - First MVP increment
4. Phase 4: User Story 5 - Persistence (T023-T027) - Makes US1 actually useful
5. Phase 5: User Story 2 - Complete (T028-T031) - Second MVP increment
6. Phase 6: User Story 3 - Delete (T032-T034) - Third MVP increment
7. Phase 7: User Story 4 - Filter (T035-T041) - Final feature increment
8. Phase 8: Polish (T042-T052) - Quality and documentation

### Within Each Phase

- **Setup**: Sequential (T001 → T002 → T003)
- **Foundational**: Mostly sequential, but T013-T015 (utility functions) can run in parallel
- **User Story 1**: Sequential (storage → rendering → task management → event handlers → init)
- **User Story 5**: T023-T024 can run in parallel, then T025-T027 sequential
- **User Story 2**: Sequential (function → handler → update renders)
- **User Story 3**: Sequential (function → handler → update renders)
- **User Story 4**: T035-T038 functions can be written in parallel, then T039-T041 sequential updates
- **Polish**: T042-T044 comments can run in parallel, T045-T052 are testing/validation tasks

### Parallel Opportunities

All tasks marked [P] can run in parallel with each other within their phase:
- **Foundational**: T013, T014, T015 (utility functions)
- **User Story 5**: T023, T024 (storage functions)
- **Polish**: T042, T043, T044 (documentation)

---

## Parallel Example: Utility Functions (Foundational Phase)

```bash
# These three utility functions can be written simultaneously:
Task T013: "Add utility function generateTaskId() in <script> tag in index.html"
Task T014: "Add utility function isValidTaskText(text) in <script> tag in index.html"
Task T015: "Add utility function escapeHTML(text) in <script> tag in index.html"

# They have no dependencies on each other and serve different purposes
```

---

## Implementation Strategy

### MVP First (User Story 1 + 5 Only)

1. Complete Phase 1: Setup (T001-T003)
2. Complete Phase 2: Foundational (T004-T015) - CRITICAL
3. Complete Phase 3: User Story 1 (T016-T022) - Basic create/view
4. Complete Phase 4: User Story 5 (T023-T027) - Add persistence
5. **STOP and VALIDATE**: Test US1+US5 independently using quickstart.md
6. **Result**: Functional todo app that persists - ready to demo/deploy as MVP

### Full Feature Delivery (All User Stories)

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Basic task creation works
3. Add User Story 5 → Test independently → Tasks persist across sessions (MVP!)
4. Add User Story 2 → Test independently → Can mark tasks complete
5. Add User Story 3 → Test independently → Can delete tasks
6. Add User Story 4 → Test independently → Can filter task views
7. Phase 8: Polish → Final quality pass
8. Each story adds value without breaking previous stories

### Incremental Testing Strategy

After each user story phase, run the corresponding test from quickstart.md:
- After US1 (Phase 3): Test 1 - Create Tasks
- After US5 (Phase 4): Test 5 - Persistent Storage
- After US2 (Phase 5): Test 2 - Mark Tasks Complete
- After US3 (Phase 6): Test 3 - Delete Tasks
- After US4 (Phase 7): Test 4 - Filter Tasks
- After Phase 8: Run all edge case tests (T046-T050)

---

## File Organization

Since this is a single-file application, all tasks modify `index.html`:

**index.html structure**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- T001-T003: Basic setup -->
  <!-- T005-T011: CSS styles -->
</head>
<body>
  <!-- T004: HTML structure -->
  <main>
    <!-- Form, filters, task list -->
  </main>

  <script>
    // T012: State variables

    // T013-T015, T025: Utility functions

    // T016-T017, T023-T024: Storage functions

    // T019, T028, T032: Task management functions

    // T035-T036: Filter functions

    // T018, T037: Rendering functions

    // T020, T029, T033, T038: Event handlers

    // T021: Initialization

    // T022: DOMContentLoaded listener
  </script>
</body>
</html>
```

---

## Constitution Compliance Checkpoints

Per project constitution, verify compliance during implementation:

**Principle I: Code Simplicity**
- T045: Review function names for clarity
- All functions should be single-purpose and easy to understand

**Principle II: Comprehensive Documentation**
- T042-T044: Add comment blocks and inline comments
- Every function must have purpose, parameters, return value documented

**Principle III: Modern JavaScript with Controlled Complexity**
- Use only approved ES6+ features: const, let, template literals, arrow functions, .filter(), .map(), .forEach()
- Avoid: .reduce(), generators, proxies, complex destructuring

**Principle IV: Universal Accessibility**
- T046: Keyboard navigation test
- T047: Screen reader test
- T048: Color contrast verification
- All tasks in Foundational phase (T004-T011) include accessibility requirements

**Principle V: Minimal & Clean Design**
- Single file structure (no external dependencies)
- T052: Organize CSS with clear sections
- Clean, flat JavaScript structure (no deep nesting)

---

## Notes

- All tasks modify the single `index.html` file at repository root
- [P] markers indicate tasks that can run in parallel (different logical sections)
- [Story] labels (US1-US5) map each task to its user story for traceability
- Each user story phase ends with a checkpoint for independent testing
- Foundational phase (T004-T015) is CRITICAL and blocks all user story work
- No automated tests - using manual testing per quickstart.md
- Comments are MANDATORY per constitution - Phase 8 ensures comprehensive documentation
- Stop at any checkpoint to validate story independently per quickstart.md

---

## Quick Reference: Task Count by Phase

- **Phase 1 (Setup)**: 3 tasks
- **Phase 2 (Foundational)**: 12 tasks ⚠️ BLOCKING
- **Phase 3 (US1 - Create/View)**: 7 tasks 🎯 MVP Foundation
- **Phase 4 (US5 - Persistence)**: 5 tasks 🎯 MVP Complete
- **Phase 5 (US2 - Complete)**: 4 tasks
- **Phase 6 (US3 - Delete)**: 3 tasks
- **Phase 7 (US4 - Filter)**: 7 tasks
- **Phase 8 (Polish)**: 11 tasks

**Total**: 52 tasks

**MVP Scope** (US1 + US5): 27 tasks (Setup + Foundational + US1 + US5)
**Full Feature Set**: All 52 tasks
