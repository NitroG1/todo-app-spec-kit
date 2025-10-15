# Implementation Plan: Priority Levels for Todos

**Branch**: `003-add-priority-levels` | **Date**: 2025-10-14 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `/specs/003-add-priority-levels/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Add priority levels (High, Medium, Low) to todos with visual indicators (colors and labels). Users can set priority when creating todos, edit priority on existing todos, and filter the list by priority level. The feature uses localStorage for persistence and follows the existing vanilla JavaScript architecture with no external dependencies.

## Technical Context

**Language/Version**: JavaScript ES6+ (vanilla, no frameworks)
**Primary Dependencies**: None (zero external dependencies maintained)
**Storage**: Browser localStorage API with todo object schema extension
**Testing**: Manual testing with accessibility verification (keyboard navigation + screen readers)
**Target Platform**: Modern web browsers (Chrome, Firefox, Safari, Edge - ES6+ support)
**Project Type**: Single-file web application (index.html with embedded CSS and JavaScript)
**Performance Goals**: Instant UI updates (<100ms for priority changes), handle 100+ todos without degradation
**Constraints**: Must maintain beginner-friendly code with extensive comments, zero external dependencies, WCAG AA accessibility compliance
**Scale/Scope**: Single-user local storage, ~100-500 todos typical use case

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

### Initial Check (Pre-Research)

**Principle I: Code Simplicity & Beginner-Friendliness**
- ✅ **PASS**: Feature uses straightforward state management (priority string stored on todo object)
- ✅ **PASS**: UI updates follow existing patterns (no complex state synchronization)
- ✅ **PASS**: Priority filtering uses simple array `.filter()` methods already in codebase

**Principle II: Comprehensive Documentation via Comments**
- ✅ **PASS**: Will follow existing comment style (every function documented, inline explanations)
- ✅ **PASS**: Priority logic will include educational comments explaining why certain choices were made

**Principle III: Modern JavaScript with Controlled Complexity**
- ✅ **PASS**: Uses approved features only: `let/const`, arrow functions, template literals, array methods (`.filter()`, `.map()`)
- ✅ **PASS**: No restricted features required (no generators, proxies, reduce, etc.)

**Principle IV: Universal Accessibility**
- ✅ **PASS**: Priority selection will use semantic HTML (`<select>` or radio buttons)
- ✅ **PASS**: Visual indicators dual-coded (color + text label) per WCAG guidelines
- ✅ **PASS**: All controls keyboard navigable
- ✅ **PASS**: Screen reader announcements via ARIA live regions (existing pattern)
- ✅ **PASS**: Color contrast meets WCAG AA (red/orange/green for High/Medium/Low)

**Principle V: Minimal & Clean Design**
- ✅ **PASS**: Follows existing inline styles pattern (no CSS framework needed)
- ✅ **PASS**: Priority UI integrates into existing form/list layout
- ✅ **PASS**: No new dependencies introduced

### Design Philosophy Check

**Visual Aesthetic**
- ✅ **PASS**: Will use subtle badges/tags for priority labels (consistent with Apple HIG card design)
- ✅ **PASS**: Color-coded indicators with generous whitespace
- ✅ **PASS**: Smooth transitions on filter changes (existing pattern: `transition-all duration-200`)

**Technical Implementation**
- ✅ **PASS**: No Tailwind CSS or shadcn/ui (project uses vanilla CSS per constitution update)
- ✅ **PASS**: Design specifications followed: rounded corners, subtle shadows, system fonts

**Component Patterns**
- ✅ **PASS**: Priority badges use muted colors (not bright/saturated)
- ✅ **PASS**: Generous padding for touch targets (44x44px minimum)
- ✅ **PASS**: Hover states with subtle background changes

### Verdict

**Status**: ✅ **APPROVED - No violations**

All constitutional principles satisfied. Feature aligns with existing architecture and complexity levels. No justification needed in Complexity Tracking table.

## Project Structure

### Documentation (this feature)

```
specs/003-add-priority-levels/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command) - N/A for single-file app
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)

```
index.html               # Single-file application with embedded CSS/JS
├── <head>
│   └── <style>         # CSS styles (will add priority badge/filter styles)
└── <body>
    ├── HTML structure  # Will add priority dropdown/selector to form
    └── <script>        # JavaScript (will add priority logic)
        ├── State Variables
        │   └── currentPriorityFilter (new)
        ├── Utility Functions
        │   ├── getPriorityColor() (new)
        │   └── getPriorityLabel() (new)
        ├── Storage Functions
        │   └── migratePrioritySchema() (new)
        ├── Rendering Functions
        │   ├── renderPriorityBadge() (new)
        │   └── renderPriorityFilterButtons() (new)
        ├── Priority Management Functions (new section)
        │   ├── setTodoPriority()
        │   └── validatePriority()
        ├── Filter Functions
        │   └── filterTodosByPriority() (new)
        └── Event Handlers
            └── handlePriorityFilterClick() (new)
```

**Structure Decision**: Single-file web application maintained. All priority logic added inline following existing architectural patterns (state management, localStorage persistence, DOM manipulation). No separation into modules to maintain beginner-friendly structure per Constitution Principle I.

## Complexity Tracking

*No violations - this section left empty per template instructions*
