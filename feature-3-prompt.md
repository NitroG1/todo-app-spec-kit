FEATURE 3: Category Tags - Sub-Agent Delegation

I need you to implement a Category Tags feature for my todo app.

FEATURE REQUIREMENTS:
- Users can add multiple tags to any todo (e.g., "work", "personal", "urgent")
- Maximum 5 tags per todo
- Each tag has a 20-character limit
- Clicking a tag filters the todo list to show only items with that tag
- Multiple tags can be selected (AND filter - show todos with ALL selected tags)
- Tags display as small, rounded pills next to the todo text
- A "clear filters" option when any tags are selected

USER EXPERIENCE:
- Adding tag: Click "+ Tag" button → inline input appears → type tag name → press Enter
- Show character count as user types (e.g., "12/20")
- Disable adding more tags when limit of 5 is reached
- Removing tag: Click X icon on the tag pill
- Tag suggestions: Show existing tags from ALL todos as user types (autocomplete)
- Visual feedback: Selected filter tags should have distinct highlighted styling
- Show validation messages: "Tag must be 20 characters or less" and "Maximum 5 tags per todo"

TECHNICAL CONSTRAINTS:
- Store tags as an array in each todo object: {id, text, completed, dueDate, priority, tags: []}
- Tags are stored as lowercase for consistency (display with proper case)
- Update localStorage schema to include tags array
- Use SINGLE neutral color for all tags: #64748b (slate-500) background, white text
- Add tags UI beneath the todo text, above due date/priority
- Write tests for: adding tags (within limits), character limit validation, 5-tag limit, removing tags, filtering by tags

QUALITY STANDARDS:
- Maintain existing test coverage (add new tests, don't break old ones)
- Mobile-responsive (tags should wrap on small screens)
- No breaking changes to Features 1 & 2
- Handle edge cases: empty tags, duplicate tags on same todo, special characters

Create an implementation plan with specific file changes and component structure. Wait for my approval before coding.