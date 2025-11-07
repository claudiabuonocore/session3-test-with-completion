# Product Requirements Document (PRD) - TODO App Enhancement

## 1. Overview

We are upgrading the basic TODO app to support due dates, priorities, and filters so users can better organize tasks and manage their workload more effectively. This enhancement makes the app more practical for real-world use without adding unnecessary complexity.

---

## 2. MVP Scope

### Core Features
- **Due Dates**: Add optional `dueDate` field to tasks (ISO `YYYY-MM-DD` format)
  - Invalid date values should be ignored and treated as absent
  - Due dates are optional; tasks can exist without them
  
- **Priority Levels**: Add `priority` field with three levels
  - Enum values: `P1` (High), `P2` (Medium), `P3` (Low)
  - Default priority: `P3`
  
- **Filters**: Implement three filter tabs for task views
  - **All**: Display all tasks, including completed ones
  - **Today**: Display only incomplete tasks due today
  - **Overdue**: Display only incomplete tasks past their due date
  
- **Storage**: Use local storage only (no backend changes required)

### Data Model
- `title`: Required string
- `completed`: Boolean (existing)
- `priority`: Enum (`P1 | P2 | P3`), default `P3`
- `dueDate`: Optional ISO string (`YYYY-MM-DD`)

### Validation Rules
- Task title is required
- Priority defaults to `P3` if not specified
- Invalid due dates are silently ignored

---

## 3. Post-MVP Scope

### Visual Enhancements
- **Overdue Task Highlighting**: Display overdue tasks with red background/highlight for visual distinction
- **Priority Color Badges**: Color-code priority levels
  - P1 (High): Red badge
  - P2 (Medium): Orange badge
  - P3 (Low): Gray badge

### Sorting Behavior
When tasks are displayed, sort by the following priority order:
1. Overdue status first (overdue tasks appear at top)
2. Priority level (P1 → P2 → P3)
3. Due date (ascending/soonest first)
4. Tasks without due dates appear last

---

## 4. Out of Scope

- Notifications or reminders
- Recurring tasks
- Multi-user support or collaboration features
- Keyboard navigation or advanced accessibility features (beyond basic WCAG compliance)
- External storage or backend synchronization (local storage only)
- Descriptions or additional task metadata (beyond title, due date, priority, completion status)
- Task categories or tags

