# Implementation Plan: Add Due Dates to Todos

**Branch**: `002-add-due-dates` | **Date**: 2025-10-14 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/002-add-due-dates/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Extend the existing todo list application to support optional due dates on tasks. Users can set, edit, and remove ISO 8601-formatted due dates (YYYY-MM-DD) via inline editing with an edit icon. The feature includes visual indicators (color + icons) for overdue and due-today tasks, a toolbar above the todo list with sort controls (ascending/descending) and filter options (overdue, due today, due this week, no due date). The system automatically migrates existing todos by adding dueDate: null on first load. All interactions remain keyboard accessible and screen reader compatible per constitution Principle IV.

## Technical Context

**Language/Version**: JavaScript ES6+ (vanilla, no frameworks)
**Primary Dependencies**: None (zero external dependencies - maintained from feature 001)
**Storage**: Browser localStorage API with ISO 8601 date strings (YYYY-MM-DD format)
**Testing**: Manual testing initially; keyboard navigation and screen reader testing required
**Target Platform**: Modern web browsers (Chrome 4+, Firefox 3.5+, Safari 4+, Edge 12+, IE 8+)
**Project Type**: Single-page web application (client-side only) - extends existing index.html
**Performance Goals**:
  - Date operations (add/edit/remove) < 100ms response time
  - Sort and filter operations < 50ms
  - Visual indicator rendering instant
  - Support 1000+ tasks with due dates without degradation
**Constraints**:
  - Client-side only (no server/backend)
  - localStorage limit 5-10MB (dates add minimal overhead ~10 bytes per todo)
  - Must work offline after initial load
  - Must meet WCAG AA accessibility standards (color + icon indicators required)
  - Must maintain backward compatibility with existing todos
**Scale/Scope**:
  - Single-user per browser
  - Estimated +150-250 additional lines of JavaScript
  - +50-100 additional lines of CSS for toolbar and visual indicators
  - Extends existing single HTML file (index.html)
  - Support up to 1000 tasks with due dates

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

### Principle I: Code Simplicity & Beginner-Friendliness
✅ **PASS** - Feature uses simple date operations with native JavaScript Date objects and ISO 8601 strings. Inline edit pattern, toolbar controls, and visual indicators all use straightforward DOM manipulation.

**Compliance Plan**:
- Use simple functions: `setDueDate()`, `removeDueDate()`, `getDueDateStatus()`, `sortByDueDate()`, `filterByDueDate()`
- ISO 8601 format enables simple string comparison and sorting
- Edit icon reveals date picker inline (show/hide logic only)
- Toolbar uses standard button/dropdown patterns
- Visual indicators use CSS classes + icon text/SVG (no complex rendering)

### Principle II: Comprehensive Documentation via Comments
✅ **PASS** - All date-related functions will have detailed comment blocks. ISO 8601 format choice, date comparison logic, and migration strategy will be explained inline.

**Compliance Plan**:
- Every date function gets detailed comments explaining ISO 8601 usage
- Date comparison and status logic (overdue/today/future) explained step-by-step
- Migration function commented to explain backward compatibility approach
- Visual indicator CSS classes documented with purpose
- Toolbar interaction patterns explained

### Principle III: Modern JavaScript with Controlled Complexity
✅ **PASS** - Using only approved ES6+ features: `const`/`let`, template literals, arrow functions, `.filter()`, `.map()`, `.sort()`.

**Approved Features in Use**:
- `const` and `let` for variable declarations
- Template literals for date display formatting
- Arrow functions for event listeners and sort/filter callbacks
- `.filter()` for filtering by due date criteria
- `.sort()` for date-based ordering (lexicographic sort of ISO 8601 strings)
- `.map()` for rendering updated todo list with due dates

**Restricted Features Avoided**:
- No generators, proxies, symbols
- No complex destructuring
- No `.reduce()` (using simple loops for any aggregation)

### Principle IV: Universal Accessibility
✅ **PASS** - Full WCAG AA compliance maintained. Color + icon indicators meet "no color-only" requirement. All toolbar controls keyboard accessible. Screen reader announcements for date changes.

**Compliance Plan**:
- Native HTML `<input type="date">` provides accessible date picker
- Edit icon button keyboard accessible with proper ARIA label
- Toolbar controls (sort dropdown, filter buttons) fully keyboard navigable
- Visual indicators use BOTH color AND icons (⚠️ overdue, 📅 today)
- ARIA labels on icon-only elements ("Edit due date", "Warning: overdue", "Due today")
- ARIA live region announces date additions/changes/removals
- Filter/sort changes announced to screen readers

### Principle V: Minimal & Clean Design
✅ **PASS** - Zero new dependencies. Extends existing single-file structure. Toolbar adds clean visual hierarchy. Icons use simple text/emoji or inline SVG (no icon fonts).

**Compliance Plan**:
- Zero external dependencies maintained
- Continues single HTML file structure (extends index.html)
- Toolbar positioned above todo list with clean layout
- Visual indicators use subtle colors and simple icons
- CSS organized in clear sections (toolbar, date display, visual indicators)
- No unused styles
- Migration logic runs once, transparently

### Gate Status: ✅ ALL GATES PASS

No constitutional violations. No complexity justification required. Ready to proceed to Phase 0.

## Project Structure

### Documentation (this feature)

```
specs/002-add-due-dates/
├── spec.md                  # Feature specification
├── plan.md                  # This file (/speckit.plan command output)
├── research.md              # Phase 0 output (/speckit.plan command)
├── data-model.md            # Phase 1 output (/speckit.plan command)
├── quickstart.md            # Phase 1 output (/speckit.plan command)
├── contracts/               # Phase 1 output (/speckit.plan command)
│   └── function-interfaces.md
├── checklists/
│   └── requirements.md      # Quality checklist (from /speckit.specify)
└── tasks.md                 # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)

```
todo-app/
├── index.html               # Main HTML file - EXTENDS with due date features
├── README.md                # Project documentation - UPDATE with due date info
└── specs/                   # Feature specifications (planning artifacts)
    ├── 001-build-a-todo/
    │   └── [existing spec files]
    └── 002-add-due-dates/   # This feature
        ├── spec.md
        ├── plan.md          # This file
        ├── research.md
        ├── data-model.md
        ├── quickstart.md
        └── contracts/
            └── function-interfaces.md
```

**Structure Decision**: Extends existing single-file web application

This feature extends the existing `index.html` from feature 001-build-a-todo. The single-file approach is maintained for:

- **Consistency**: Matches existing architecture from feature 001
- **Beginner accessibility**: All code remains in one file
- **Zero build process**: No changes to deployment model
- **Minimal complexity**: No new files or module systems

The due date functionality will be added to the existing `<script>` section in index.html with:
- New date-related functions clearly commented and grouped
- Extended Todo data model (adds `dueDate` field)
- Toolbar HTML added above the existing todo list
- CSS for toolbar, date display, and visual indicators added to existing `<style>` section
- Migration logic in initialization code

## Complexity Tracking

*Fill ONLY if Constitution Check has violations that must be justified*

No constitutional violations. This section is not applicable.

---

## Post-Design Constitution Re-Evaluation

*Re-check after Phase 1 design artifacts completed*

**Date**: 2025-10-14
**Status**: ✅ ALL PRINCIPLES CONFIRMED

### Review of Design Artifacts

After completing research.md, data-model.md, contracts/function-interfaces.md, and quickstart.md, all constitutional principles remain satisfied:

#### Principle I: Code Simplicity & Beginner-Friendliness ✅
- **Confirmed**: All 17 function interfaces use simple, descriptive names
- **Confirmed**: Data model extends existing structure with single optional field
- **Confirmed**: ISO 8601 string format enables simple lexicographic sorting
- **Confirmed**: No complex patterns introduced (no classes, decorators, or advanced closures)

#### Principle II: Comprehensive Documentation via Comments ✅
- **Confirmed**: Function interfaces documented with purpose, parameters, returns
- **Confirmed**: Research decisions documented with rationale and alternatives
- **Confirmed**: Quickstart provides step-by-step implementation guide
- **Confirmed**: Data model validation rules clearly specified

#### Principle III: Modern JavaScript with Controlled Complexity ✅
- **Confirmed**: Only approved ES6+ features in function designs (const/let, template literals, arrow functions)
- **Confirmed**: No restricted features (.reduce(), generators, proxies) planned
- **Confirmed**: Array operations use simple .filter(), .map(), .sort()
- **Confirmed**: Date comparisons use straightforward Date object methods

#### Principle IV: Universal Accessibility ✅
- **Confirmed**: Dual-coded visual indicators (color + icon) specified
- **Confirmed**: Native HTML date input provides built-in accessibility
- **Confirmed**: ARIA labels planned for all icon-only elements
- **Confirmed**: ARIA live region for screen reader announcements
- **Confirmed**: Keyboard navigation tested in quickstart guide

#### Principle V: Minimal & Clean Design ✅
- **Confirmed**: Zero external dependencies maintained (no date picker library)
- **Confirmed**: Single-file structure preserved (extends existing index.html)
- **Confirmed**: Clean toolbar layout with standard HTML elements
- **Confirmed**: CSS organized in clear sections (toolbar, indicators, date display)
- **Confirmed**: Migration logic simple and transparent

### Final Gate Status: ✅ APPROVED FOR IMPLEMENTATION

All design decisions reinforce constitutional compliance. No new complexity introduced. The following artifacts are complete and ready:

- ✅ research.md - 8 key decisions documented with rationale
- ✅ data-model.md - Todo entity extended, storage format specified, migration strategy defined
- ✅ contracts/function-interfaces.md - 17 function interfaces specified with clear signatures
- ✅ quickstart.md - 8-phase implementation guide with testing procedures

Ready to proceed to `/speckit.tasks` for task generation.
