# Epics and User Stories - TODO App Enhancement

This document breaks down the MVP and Post-MVP requirements from `docs/prd-todo.md` into epics and stories with acceptance criteria.

## MVP Epics and Stories

### Epic: Task Due Dates

**Story: Add due date field to task model**
- Acceptance Criteria:
  - Task model includes a `dueDate` property that stores ISO 8601 formatted date strings (YYYY-MM-DD)
  - `dueDate` is optional; tasks can be created without a due date
  - Invalid date formats are ignored and treated as if no due date was provided
  - Existing tasks without a due date continue to function normally
- Technical Requirements:
  - Update task object structure to include `dueDate: string | null | undefined`
  - Add validation logic to accept only ISO 8601 date format (YYYY-MM-DD)
  - Implement null-coalescing or optional chaining to handle missing `dueDate`
  - Ensure backward compatibility: tasks without `dueDate` should not cause errors

**Story: Create due date input in task form**
- Acceptance Criteria:
  - Task form includes a date input field for setting due dates
  - Date input accepts ISO 8601 format (YYYY-MM-DD)
  - Users can leave the due date empty (optional)
  - Form allows editing due dates on existing tasks
  - Invalid dates are rejected or silently ignored
- Technical Requirements:
  - Add HTML `<input type="date">` element to TaskForm component
  - Implement onChange handler to capture date value
  - Add date validation function using JavaScript `Date` or date parsing library
  - Clear or reset date field when form is reset
  - Pass `dueDate` value to task creation/update function

**Story: Display due date on task list**
- Acceptance Criteria:
  - Due date is displayed on each task in the task list
  - Date is formatted in a user-friendly way (e.g., "Dec 15, 2025")
  - Tasks without due dates show no date or a placeholder (e.g., "No due date")
  - Due date is visible in all filter views
- Technical Requirements:
  - Create date formatting function using `Intl.DateTimeFormat` or date library (e.g., date-fns)
  - Conditionally render due date in TaskList item component
  - Handle null/undefined `dueDate` gracefully with fallback text
  - Apply consistent date formatting across all filter views

**Story: Validate and handle invalid date formats**
- Acceptance Criteria:
  - Invalid date strings (e.g., "2025-13-01", "invalid") are rejected
  - Invalid dates do not cause errors; they are treated as absent
  - Users receive clear feedback when entering an invalid date format
  - The task form continues to function even if an invalid date is entered
- Technical Requirements:
  - Implement regex validation for YYYY-MM-DD format
  - Use `Date.parse()` or validator library to check date validity
  - Add try-catch or error handling around date parsing
  - Display validation error message in form UI
  - Prevent form submission with invalid dates, or sanitize input before save

---

### Epic: Task Priority Levels

**Story: Add priority field to task model with P1, P2, P3 levels**
- Acceptance Criteria:
  - Task model includes a `priority` property with enum values: P1, P2, P3
  - Default priority is P3 when not explicitly set
  - Priority is required for all tasks
  - Valid priorities are strictly: P1 (High), P2 (Medium), P3 (Low)
  - Existing tasks default to P3 priority
- Technical Requirements:
  - Update task object structure with `priority: 'P1' | 'P2' | 'P3'`
  - Define priority enum or constants: `P1`, `P2`, `P3`
  - Set default value to `P3` when creating new tasks
  - Validate priority against allowed values
  - Migrate existing tasks to include `priority: 'P3'`

**Story: Create priority selector in task form**
- Acceptance Criteria:
  - Task form includes a priority selector (dropdown, radio buttons, or similar)
  - Priority selector displays all three levels: P1, P2, P3
  - Users can set priority when creating a new task
  - Users can change priority when editing an existing task
  - Default selection is P3
- Technical Requirements:
  - Add select or radio button group to TaskForm component
  - Populate options with P1, P2, P3 values
  - Set default selected value to P3
  - Implement onChange handler to capture priority selection
  - Pass selected priority to task creation/update function

**Story: Display priority level on task list**
- Acceptance Criteria:
  - Priority level is visible on each task in the task list
  - Priority is displayed in a clear, scannable format (e.g., label or badge)
  - Priority is shown in all filter views
  - Priority information does not clutter the task title
- Technical Requirements:
  - Render priority badge/label in TaskList item component
  - Map priority value (P1/P2/P3) to display label (High/Medium/Low)
  - Position priority display appropriately in task layout
  - Apply consistent rendering across all filter views

**Story: Set default priority to P3 for new tasks**
- Acceptance Criteria:
  - When a new task is created without an explicit priority, it defaults to P3
  - Default priority is applied automatically when saving the task
  - Users can override the default priority in the form before saving
- Technical Requirements:
  - In task creation function, use nullish coalescing (`priority ?? 'P3'`) for default
  - Ensure form initializes priority field with P3 value
  - Override default only if user explicitly selects different priority

---

### Epic: Task Filtering

**Story: Create filter tabs (All, Today, Overdue)**
- Acceptance Criteria:
  - Three filter tabs are displayed: All, Today, Overdue
  - Filter tabs are clearly labeled and easy to identify
  - Tabs are always visible and accessible to users
  - Only one tab is active at a time
- Technical Requirements:
  - Create Tab component or UI control for filter selection
  - Define state to track active filter ('All' | 'Today' | 'Overdue')
  - Implement click handlers to switch between filters
  - Apply active/inactive styling to distinguish selected tab
  - Render three tab buttons/elements with descriptive labels

**Story: Implement All filter view**
- Acceptance Criteria:
  - All filter displays all tasks in the task list
  - All tasks are shown, including completed and incomplete tasks
  - All tasks are shown, regardless of due date
  - Tasks are displayed without any filtering applied
- Technical Requirements:
  - Create filter function that returns all tasks without filtering
  - Pass all tasks to TaskList component when 'All' filter is active
  - Ensure no conditional logic removes tasks in this view

**Story: Implement Today filter view showing incomplete tasks due today**
- Acceptance Criteria:
  - Today filter displays only incomplete tasks
  - Only tasks with a due date of today are shown
  - Completed tasks are hidden in this view
  - Tasks without a due date are not shown in this view
  - "Today" is calculated based on the current date in the user's timezone
- Technical Requirements:
  - Create filter function that checks: `completed === false && dueDate === today`
  - Get today's date using `new Date().toISOString().split('T')[0]` (YYYY-MM-DD)
  - Compare task `dueDate` with today's date string
  - Return filtered array to TaskList component
  - Handle timezone considerations (local date, not UTC)

**Story: Implement Overdue filter view showing incomplete overdue tasks**
- Acceptance Criteria:
  - Overdue filter displays only incomplete tasks
  - Only tasks with a due date earlier than today are shown
  - Completed tasks are hidden in this view
  - Tasks without a due date are not shown in this view
  - Overdue status is calculated based on the current date
- Technical Requirements:
  - Create filter function that checks: `completed === false && dueDate < today`
  - Use string comparison for dates in YYYY-MM-DD format (lexicographic order works)
  - Exclude tasks where `dueDate` is null/undefined
  - Return filtered array to TaskList component

**Story: Persist active filter selection**
- Acceptance Criteria:
  - The active filter tab selection is saved to local storage
  - When the user returns to the app, the previously selected filter is active
  - The active filter is maintained across page refreshes
  - Default filter on first load is "All"
- Technical Requirements:
  - Store active filter in local storage using key like `activeFilter`
  - On filter change, call `localStorage.setItem('activeFilter', filterName)`
  - On app load, retrieve filter from `localStorage.getItem('activeFilter')`
  - Fall back to 'All' if no stored value exists
  - Update state with retrieved or default filter value

---

### Epic: Local Storage Integration

**Story: Save tasks with due date and priority to local storage**
- Acceptance Criteria:
  - All tasks, including due date and priority, are saved to browser local storage
  - Tasks are saved immediately after any create, update, or delete action
  - Local storage format includes `dueDate` (ISO string) and `priority` (P1/P2/P3)
  - Data persists across browser sessions and page refreshes
  - No backend or external storage is used
- Technical Requirements:
  - Use `localStorage.setItem('tasks', JSON.stringify(tasksArray))` to save
  - Include all task properties: `id`, `title`, `completed`, `dueDate`, `priority`
  - Call save function after every create, update, or delete operation
  - Use JSON serialization to handle object storage
  - No server-side communication required

**Story: Load tasks with due date and priority from local storage**
- Acceptance Criteria:
  - When the app loads, all tasks are retrieved from local storage
  - Tasks are loaded with their due date and priority intact
  - If local storage is empty, the app loads with no tasks
  - Loading does not cause errors or data loss
- Technical Requirements:
  - Create load function using `localStorage.getItem('tasks')`
  - Parse JSON string: `JSON.parse(localStorage.getItem('tasks') || '[]')`
  - Call load function in app initialization (e.g., `useEffect` with empty dependency)
  - Set loaded tasks to state
  - Handle null/undefined gracefully, defaulting to empty array

**Story: Maintain backward compatibility with existing task data**
- Acceptance Criteria:
  - Existing tasks without due date or priority continue to work
  - Tasks migrated from old format receive default values (P3 priority, no due date)
  - Old task data is not deleted or corrupted during migration
  - Users can continue using the app even if switching between old and new versions
- Technical Requirements:
  - In load function, map old task format to new format with defaults
  - Use logic like: `{ ...oldTask, priority: oldTask.priority || 'P3', dueDate: oldTask.dueDate || null }`
  - Ensure tasks with missing `priority` are assigned `'P3'`
  - Ensure tasks with missing `dueDate` are assigned `null` or `undefined`
  - Do not delete or overwrite old data; only add missing properties

---

## Post-MVP Epics and Stories

### Epic: Visual Highlighting and Badges

**Story: Highlight overdue tasks with red background**
- Acceptance Criteria:
  - Overdue tasks are visually highlighted with a red background or red styling
  - Highlighting is applied consistently across all views (All, Today, Overdue filters)
  - Highlighting does not interfere with task readability
  - Non-overdue tasks do not have red highlighting
  - The red color is visually distinct and accessible
- Technical Requirements:
  - Create utility function `isOverdue(dueDate)` that compares dueDate with today
  - In TaskList item, conditionally apply CSS class based on overdue status
  - Apply red background color (e.g., `#ffebee` or similar per UI guidelines)
  - Use CSS classes or inline styles to highlight overdue tasks
  - Ensure text contrast meets WCAG AA standards on red background

**Story: Display color-coded priority badges (Red for P1, Orange for P2, Gray for P3)**
- Acceptance Criteria:
  - P1 tasks display a red badge or indicator
  - P2 tasks display an orange badge or indicator
  - P3 tasks display a gray badge or indicator
  - Priority badges are consistently displayed in all views
  - Badge colors are accessible and meet WCAG color contrast requirements
  - Badges are clearly labeled or visually distinct by color
- Technical Requirements:
  - Create `PriorityBadge` component that renders badge for a given priority
  - Map priority to color: P1 → red (#f44336), P2 → orange (#ff9800), P3 → gray (#9e9e9e)
  - Display priority label text on badge (e.g., "High", "Medium", "Low")
  - Use CSS or Material-UI Badge/Chip component for consistent styling
  - Apply consistent badge sizing and positioning across all views

**Story: Apply visual styling consistently across all filter views**
- Acceptance Criteria:
  - Red highlighting for overdue tasks is applied in All, Today, and Overdue views
  - Color-coded priority badges appear in all views
  - Styling rules are consistent regardless of filter applied
  - No visual inconsistencies between views
- Technical Requirements:
  - Define CSS styles in a shared stylesheet or CSS module
  - Apply overdue highlighting styles in TaskList component (used by all views)
  - Apply priority badge styles in shared component (used by all views)
  - Use consistent class names or style props across filter views
  - Test visual consistency across All, Today, and Overdue filters

---

### Epic: Task Sorting

**Story: Sort tasks by overdue status first**
- Acceptance Criteria:
  - Overdue tasks appear at the top of the task list
  - Non-overdue tasks appear below overdue tasks
  - Sorting is applied in all filter views
  - Overdue status takes precedence over all other sort criteria
- Technical Requirements:
  - Create sort comparator function: `(a, b) => isOverdue(b.dueDate) - isOverdue(a.dueDate)`
  - Call sort on filtered tasks before rendering in TaskList
  - Apply sort in all filter views (All, Today, Overdue)
  - Ensure sort is stable and doesn't break on subsequent sorts

**Story: Sort tasks by priority level (P1 → P2 → P3)**
- Acceptance Criteria:
  - Among tasks with the same overdue status, P1 tasks appear first
  - P2 tasks appear after P1 tasks
  - P3 tasks appear last
  - Priority sorting is applied within each overdue/non-overdue group
  - Sorting applies across all filter views
- Technical Requirements:
  - Create priority order map: `{ P1: 0, P2: 1, P3: 2 }`
  - Add priority comparison to sort function after overdue check
  - Use: `priorityOrder[a.priority] - priorityOrder[b.priority]`
  - Apply as secondary sort criterion after overdue status

**Story: Sort tasks by due date (ascending)**
- Acceptance Criteria:
  - Among tasks with the same overdue status and priority, tasks are sorted by due date
  - Soonest due dates appear first
  - Later due dates appear later
  - Sorting is ascending (earliest to latest)
  - Applies across all filter views
- Technical Requirements:
  - Add due date comparison to sort function after priority check
  - Use lexicographic string comparison for ISO dates: `a.dueDate.localeCompare(b.dueDate)`
  - Apply as tertiary sort criterion after overdue and priority
  - Handle null/undefined dueDate (should be handled by next story)

**Story: Position undated tasks last in sort order**
- Acceptance Criteria:
  - Tasks without a due date appear at the end of the task list
  - Undated tasks appear after all dated tasks regardless of priority or overdue status
  - This applies consistently across all views
  - Undated tasks do not disrupt the sort order of dated tasks
- Technical Requirements:
  - In sort comparator, check if dueDate is null/undefined first
  - Assign nullish dates a high sort value: `(a, b) => a.dueDate == null ? 1 : b.dueDate == null ? -1 : ...`
  - Apply as first criterion in multi-level sort (before overdue/priority/date)
  - Or use separate sort bucket for undated tasks and concatenate at end

