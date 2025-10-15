# Tasks: Priority Levels for Todos

**Input**: Design documents from `/specs/003-add-priority-levels/`
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, quickstart.md

**Tests**: This feature uses manual testing per Constitution standards. No automated test tasks included.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`
- **[P]**: Can run in parallel (different sections of file, no dependencies)
- **[Story]**: Which user story this task belongs to (US1, US2, US3, US4)
- Single-file application: All tasks modify index.html

## Path Conventions
- **Single-file app**: index.html at repository root (contains HTML, CSS, JavaScript)
- Tasks specify section within index.html (e.g., "in `<style>` section" or "in Utility Functions")

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Schema migration and validation functions needed by all user stories

- [ ] **T001** [SETUP] Add schema migration function `migratePrioritySchema()` in index.html Storage Functions section (after `migrateTasksSchema()`)
- [ ] **T002** [P] [SETUP] Add priority validation function `isValidPriority(priority)` in index.html Utility Functions section
- [ ] **T003** [P] [SETUP] Add priority CSS class helper `getPriorityClass(priority)` in index.html Utility Functions section
- [ ] **T004** [P] [SETUP] Add priority label helper `getPriorityLabel(priority)` in index.html Utility Functions section
- [ ] **T005** [SETUP] Call `migratePrioritySchema()` in `init()` function before loading tasks (index.html line ~1537)
- [ ] **T006** [SETUP] Add state variable `let currentPriorityFilter = 'all';` in index.html State Variables section (line ~452)

**Checkpoint**: Migration and validation infrastructure ready - all user stories can now proceed

---

## Phase 2: User Story 1 - Setting Priority on New Todos (Priority: P1) 🎯 MVP

**Goal**: Users can select priority (High, Medium, Low) when creating a new todo, with Medium as the default

**Independent Test**: Create new todos with each priority level (High, Medium, Low) and verify they are saved with correct priority values. Check localStorage to confirm priority field exists.

### CSS Styles for User Story 1

- [ ] **T007** [P] [US1] Add `.priority-select` CSS styles in index.html `<style>` section after "Due Date Display" section
- [ ] **T008** [P] [US1] Add `.priority-badge` base CSS styles in index.html `<style>` section
- [ ] **T009** [P] [US1] Add `.priority-high` CSS styles (red theme: bg #fee, border #c00, text #800)
- [ ] **T010** [P] [US1] Add `.priority-medium` CSS styles (orange theme: bg #fef3e0, border #f90, text #c70)
- [ ] **T011** [P] [US1] Add `.priority-low` CSS styles (blue theme: bg #e6f4ff, border #0066cc, text #004999)
- [ ] **T012** [P] [US1] Add `.task-item.completed .priority-badge` CSS style (opacity 0.5 for completed tasks)

### HTML Form for User Story 1

- [ ] **T013** [US1] Add priority `<select>` dropdown to task creation form in index.html (line ~399-410), with id="priority-select", three options (High, Medium selected, Low), and screen-reader label

### JavaScript for User Story 1

- [ ] **T014** [US1] Add helper function `getPriorityFromForm()` in index.html Utility Functions section to read selected priority from dropdown
- [ ] **T015** [US1] Update `addTask()` function in index.html to add `priority: getPriorityFromForm()` to new task object (line ~1126-1131)

**Checkpoint**: User Story 1 complete - users can create todos with priority selection, defaults to Medium

**Manual Test Checklist for US1**:
- [ ] Create todo with High priority → verify saved to localStorage with `priority: 'high'`
- [ ] Create todo with Medium priority → verify saved with `priority: 'medium'`
- [ ] Create todo with Low priority → verify saved with `priority: 'low'`
- [ ] Create todo without changing dropdown → verify defaults to `priority: 'medium'`
- [ ] Reload page → verify priorities persist across sessions
- [ ] Test keyboard: Tab to priority dropdown → Arrow keys to select → Enter to submit

---

## Phase 3: User Story 2 - Visual Priority Indicators (Priority: P1) 🎯 MVP

**Goal**: Each todo displays a visual indicator (color badge + text label) showing its priority level, making it easy to identify priority at a glance

**Independent Test**: Create todos with different priorities and verify each displays the correct color badge and label (High=red badge, Medium=orange badge, Low=blue badge). Visual indicators should be clearly distinguishable.

### JavaScript Rendering for User Story 2

- [ ] **T016** [US2] Update `renderTasks()` function in index.html to create priority badge element before task text (line ~854-901)
- [ ] **T017** [US2] Set priority badge attributes: className using `getPriorityClass()`, textContent using `getPriorityLabel()`, aria-label with priority value
- [ ] **T018** [US2] Append priority badge to list item after checkbox, before task text span

**Checkpoint**: User Story 2 complete - todos display visual priority indicators with colors and labels

**Manual Test Checklist for US2**:
- [ ] High priority todo shows red badge with "High" label
- [ ] Medium priority todo shows orange badge with "Med" label
- [ ] Low priority todo shows blue badge with "Low" label
- [ ] Multiple todos with different priorities display distinct badges
- [ ] Color contrast meets WCAG AA (verify with DevTools contrast checker)
- [ ] Completed todos show muted priority badges (opacity 50%)
- [ ] Test with color blindness simulator → text labels ensure information not lost

**Accessibility Test Checklist for US2**:
- [ ] Screen reader announces priority (e.g., "Priority: High")
- [ ] Color is not the only means of conveying priority (text labels present)
- [ ] Badges have sufficient contrast ratio (4.5:1 for text)

---

## Phase 4: User Story 3 - Editing Todo Priority (Priority: P2)

**Goal**: Users can update the priority of existing todos without recreating them, allowing priority to reflect changing circumstances

**Independent Test**: Edit existing todos and change their priority values, then verify changes persist after saving and page reload. Test all priority transitions (High→Medium, Medium→Low, etc.).

### CSS Styles for User Story 3

- [ ] **T019** [P] [US3] Add `.priority-editor` CSS styles in index.html `<style>` section (inline editor container)
- [ ] **T020** [P] [US3] Add `.priority-editor select` CSS styles (focus states, borders)
- [ ] **T021** [P] [US3] Add `.priority-badge:hover` and `.priority-badge:focus` CSS styles (cursor pointer, opacity, transform, outline)

### JavaScript Functions for User Story 3

- [ ] **T022** [P] [US3] Add function `setTodoPriority(todoId, priority)` in new "Priority Management Functions" section in index.html (validates, updates, saves, re-renders, announces to screen reader)
- [ ] **T023** [P] [US3] Add function `togglePriorityEditor(todoId)` in Priority Management Functions section (shows/hides editor, manages focus)
- [ ] **T024** [US3] Add function `createPriorityEditor(todoId)` in Priority Management Functions section (creates inline dropdown with current priority selected, handles change event)
- [ ] **T025** [US3] Update priority badge creation in `renderTasks()` to add click event listener calling `togglePriorityEditor(todoId)` (line ~T016 area)
- [ ] **T026** [US3] Add `id="priority-badge-${task.id}"` to priority badge for focus management
- [ ] **T027** [US3] Set priority badge `tabindex="0"` and `role="button"` for keyboard accessibility

**Checkpoint**: User Story 3 complete - users can edit priority inline by clicking badge

**Manual Test Checklist for US3**:
- [ ] Click priority badge → inline editor appears with current priority selected
- [ ] Select different priority → todo updates immediately with new badge color/label
- [ ] Priority change persists after page reload
- [ ] Focus returns to badge after closing editor (accessibility)
- [ ] Keyboard: Tab to badge → Enter to open editor → Arrow keys to select → Enter to save
- [ ] Test all transitions: High→Medium, Medium→Low, Low→High, etc.
- [ ] Edit priority on completed todo → works correctly, badge shows muted

**Accessibility Test Checklist for US3**:
- [ ] Badge has `aria-label="Priority: [value]. Click to edit."`
- [ ] Screen reader announces priority change: "Priority changed to High"
- [ ] Editor dropdown is keyboard accessible (Tab, Arrow keys, Enter)
- [ ] Focus visible on badge and dropdown (3px blue outline)

---

## Phase 5: User Story 4 - Filter Todos by Priority (Priority: P3)

**Goal**: Users can filter the todo list to show only items of a selected priority level, enabling focus on specific priorities

**Independent Test**: Create todos with mixed priorities, apply each filter option (All, High, Medium, Low), and verify only matching todos are displayed. Combine with existing filters (completion status, due date) to test filter integration.

### CSS Styles for User Story 4

- [ ] **T028** [P] [US4] Add `.priority-filter-controls` CSS styles in index.html `<style>` section (flex container with gap)
- [ ] **T029** [P] [US4] Add `.filter-btn-priority` CSS styles (base button styles, padding, colors)
- [ ] **T030** [P] [US4] Add `.filter-btn-priority:hover` CSS styles
- [ ] **T031** [P] [US4] Add `.filter-btn-priority.active` CSS styles (blue background, white text)
- [ ] **T032** [P] [US4] Add `.filter-btn-priority:focus` CSS styles (outline for accessibility)

### HTML Filter Buttons for User Story 4

- [ ] **T033** [US4] Add priority filter button group in index.html toolbar section (after due date filter controls, line ~420-436)
- [ ] **T034** [US4] Add four filter buttons with `data-filter-priority` attributes: "all" (active), "high", "medium", "low"
- [ ] **T035** [US4] Add accessible labels to filter buttons (e.g., aria-label="Filter by High priority")

### JavaScript Filter Logic for User Story 4

- [ ] **T036** [US4] Update `getFilteredTasks()` function in index.html Filter Functions section to add priority filtering after due date filter (line ~1351-1372)
- [ ] **T037** [US4] Add filter logic: if `currentPriorityFilter !== 'all'`, filter tasks by `task.priority === currentPriorityFilter`
- [ ] **T038** [US4] Add function `handlePriorityFilterClick(event)` in Event Handlers section (updates filter state, saves to sessionStorage, updates button styles, re-renders, announces to screen reader)
- [ ] **T039** [US4] Update `init()` function to restore priority filter from sessionStorage (line ~1546-1559 area)
- [ ] **T040** [US4] Update `init()` function to attach click event listeners to `.filter-btn-priority` buttons

**Checkpoint**: User Story 4 complete - users can filter by priority

**Manual Test Checklist for US4**:
- [ ] Click "High" filter → only high priority todos shown
- [ ] Click "Medium" filter → only medium priority todos shown
- [ ] Click "Low" filter → only low priority todos shown
- [ ] Click "All" filter → all todos shown regardless of priority
- [ ] Filter shows zero results → appropriate empty state message appears
- [ ] Combine filters: Active + High priority → only active high priority todos shown
- [ ] Combine filters: Overdue + High priority → only overdue high priority todos shown
- [ ] Filter persists during todo creation/editing/deletion
- [ ] Reload page → filter resets to "All" (session-only persistence verified)

**Accessibility Test Checklist for US4**:
- [ ] Screen reader announces filter change: "Filter changed to high priority only"
- [ ] Filter buttons keyboard accessible (Tab to reach, Enter/Space to activate)
- [ ] Active filter button visually distinct (blue background, white text)
- [ ] Focus visible on filter buttons (3px blue outline)

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Final refinements and documentation

- [ ] **T041** [P] [POLISH] Add comprehensive inline comments to all new priority functions explaining logic for beginners (per Constitution Principle II)
- [ ] **T042** [P] [POLISH] Review all new code against Constitution Principle I (simplicity) - refactor if any complex patterns introduced
- [ ] **T043** [P] [POLISH] Verify all CSS follows existing style patterns (transitions, hover states, focus indicators)
- [ ] **T044** [POLISH] Test complete feature flow: create with priority → view badge → edit priority → filter by priority
- [ ] **T045** [P] [POLISH] Test edge cases: rapid filter toggling, priority changes on completed todos, migration of old todos
- [ ] **T046** [P] [POLISH] Verify localStorage quota handling (try creating 500+ todos with priorities)
- [ ] **T047** [P] [POLISH] Cross-browser testing: Chrome, Firefox, Safari, Edge
- [ ] **T048** [POLISH] Full accessibility audit: keyboard-only navigation, screen reader testing (NVDA/JAWS), color contrast verification
- [ ] **T049** [P] [POLISH] Update README.md with priority feature description and usage instructions (if README exists)
- [ ] **T050** [POLISH] Run through quickstart.md testing checklist to validate all scenarios

**Checkpoint**: Feature complete and polished, ready for deployment

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - must complete first (T001-T006)
- **User Story 1 (Phase 2)**: Depends on Setup completion (needs migration, validation functions)
- **User Story 2 (Phase 3)**: Depends on US1 completion (needs priority field on todos, CSS styles)
- **User Story 3 (Phase 4)**: Depends on US2 completion (needs priority badges to be clickable)
- **User Story 4 (Phase 5)**: Depends on US1 completion (needs priority field, doesn't require US2/US3)
- **Polish (Phase 6)**: Depends on all user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Foundation - other stories depend on this
- **User Story 2 (P1)**: Depends on US1 (needs todos with priority field)
- **User Story 3 (P2)**: Depends on US2 (needs badges to click and edit)
- **User Story 4 (P3)**: Depends on US1 (needs priority field), can be done in parallel with US2/US3

### Within Each User Story

- **US1**: CSS [P] → HTML → JavaScript (sequential for JavaScript, CSS can be parallel)
- **US2**: JavaScript tasks are sequential (modify same renderTasks function)
- **US3**: CSS [P] → JavaScript functions [P] → Integration (sequential for integration)
- **US4**: CSS [P] → HTML → JavaScript (sequential for JavaScript)

### Parallel Opportunities

**Within Setup (Phase 1)**:
- T002, T003, T004 can run in parallel (different utility functions)

**Within User Story 1**:
- T007-T012 can run in parallel (different CSS rules)

**Within User Story 3**:
- T019-T021 can run in parallel (different CSS rules)
- T022-T024 can run in parallel (different JavaScript functions)

**Within User Story 4**:
- T028-T032 can run in parallel (different CSS rules)

**Within Polish**:
- T041, T042, T043, T045, T046, T047, T049 can run in parallel

---

## Parallel Example: User Story 1 CSS Styles

```bash
# Launch all CSS style tasks for User Story 1 together:
# (All modify different CSS rules in the same <style> section)

Task T007: "Add .priority-select CSS styles"
Task T008: "Add .priority-badge base CSS styles"
Task T009: "Add .priority-high CSS styles (red theme)"
Task T010: "Add .priority-medium CSS styles (orange theme)"
Task T011: "Add .priority-low CSS styles (blue theme)"
Task T012: "Add .task-item.completed .priority-badge CSS style"
```

---

## Implementation Strategy

### MVP First (User Stories 1 & 2 Only)

1. **Complete Phase 1: Setup** (T001-T006)
   - Migration, validation, state variables ready
2. **Complete Phase 2: User Story 1** (T007-T015)
   - Users can create todos with priority selection
   - **STOP and VALIDATE**: Create todos with each priority, verify localStorage
3. **Complete Phase 3: User Story 2** (T016-T018)
   - Todos display priority badges with colors
   - **STOP and VALIDATE**: Visual indicators work correctly, test accessibility
4. **Deploy MVP**: Users can create prioritized todos and see visual indicators

### Incremental Delivery

1. **Foundation**: Setup (Phase 1) → Migration and validation ready
2. **MVP**: User Story 1 + 2 → Create with priority + visual indicators → Test independently → Deploy
3. **Enhancement 1**: User Story 3 → Edit priority inline → Test independently → Deploy
4. **Enhancement 2**: User Story 4 → Filter by priority → Test independently → Deploy
5. Each story adds value without breaking previous stories

### Single Developer Strategy

**Recommended order** (due to dependencies):

1. Phase 1: Setup (T001-T006) - Foundation
2. Phase 2: User Story 1 (T007-T015) - Creation with priority
3. Phase 3: User Story 2 (T016-T018) - Visual indicators
4. Phase 4: User Story 3 (T019-T027) - Inline editing
5. Phase 5: User Story 4 (T028-T040) - Filtering
6. Phase 6: Polish (T041-T050) - Final refinements

**Alternative order** (maximize early value):

1. Phase 1: Setup
2. Phase 2: User Story 1
3. Phase 3: User Story 2
4. **Skip to Phase 5: User Story 4** (filtering) - More valuable than editing for some users
5. Return to Phase 4: User Story 3 (editing)
6. Phase 6: Polish

### Parallel Team Strategy

With two developers:

1. **Both**: Complete Phase 1 (Setup) together
2. **Developer A**: Phase 2 (US1) + Phase 3 (US2) - Creation and display
3. **Developer B**: Wait for US1, then start Phase 5 (US4) - Filtering (doesn't need US2/US3)
4. **Developer A**: Phase 4 (US3) - Editing (after US2)
5. **Both**: Phase 6 (Polish) together

---

## Task Summary

**Total Tasks**: 50

**Task Count per Phase**:
- Phase 1 (Setup): 6 tasks
- Phase 2 (User Story 1 - P1): 9 tasks
- Phase 3 (User Story 2 - P1): 3 tasks
- Phase 4 (User Story 3 - P2): 9 tasks
- Phase 5 (User Story 4 - P3): 13 tasks
- Phase 6 (Polish): 10 tasks

**Parallel Opportunities**: 23 tasks marked [P] (46% parallelizable)

**Independent Test Criteria**:
- **US1**: Create todos with each priority, verify localStorage persistence
- **US2**: Visual badges show correct colors and labels for each priority
- **US3**: Edit priority inline, verify changes persist and focus management works
- **US4**: Filter by each priority level, verify correct todos shown and filter combinations work

**Suggested MVP Scope**: Phase 1 (Setup) + Phase 2 (US1) + Phase 3 (US2)
- Estimated effort: ~15 tasks
- Delivers: Priority selection on creation + visual priority indicators
- Represents ~50% of core value with minimal complexity

---

## Notes

- **Single-file architecture**: All tasks modify index.html (different sections specified)
- **[P] tasks**: Can be done in parallel when working on different CSS rules or functions
- **No automated tests**: Manual testing per project Constitution (beginner-friendly)
- **Constitution compliance**: All tasks follow simplicity, documentation, accessibility principles
- **Checkpoint validation**: Test each user story independently before proceeding
- **Commit strategy**: Commit after each task or logical group (e.g., all CSS for US1)
- **Migration first**: T001 must complete before any todos are loaded to ensure schema compatibility
