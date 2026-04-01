# Task Management Tools

Tools for creating, listing, retrieving, updating, and stopping tasks.

---

## TaskCreateTool

**Source:** `src/tools/TaskCreateTool/prompt.ts`

### Description

```
Create a new task in the task list
```

### Prompt

```
Use this tool to create a structured task list for your current coding session.
This helps you track progress, organize complex tasks, and demonstrate thoroughness
to the user. It also helps the user understand the progress of the task and overall
progress of their requests.

## When to Use This Tool

Use this tool proactively in these scenarios:

- Complex multi-step tasks — task requires 3+ distinct steps
- Non-trivial and complex tasks — careful planning/multiple operations
- Plan mode — track plan execution
- User explicitly requests todo list
- User provides multiple tasks
- After receiving new instructions
- When you start working — mark in_progress before beginning
- After completing a task — mark completed and add follow-ups

## When NOT to Use This Tool

Skip when:
- Single straightforward task
- Trivial task
- Less than 3 trivial steps
- Purely conversational/informational

## Task Fields

- subject: brief, actionable title (imperative form)
- description: what needs to be done
- activeForm (optional): present continuous form for spinner

All tasks are created with status `pending`.

## Tips

- Create clear, specific task subjects
- Use TaskUpdate to set dependencies (blocks/blockedBy)
- Check TaskList first to avoid duplicates
```

### Agent Swarms additions (`isAgentSwarmsEnabled`)

When swarms are enabled, extra guidance is injected:

- Non-trivial tasks may be assigned to teammates
- Description should contain enough detail for another agent
- New tasks start with no owner; use `TaskUpdate.owner` to assign

---

## TaskGetTool

**Source:** `src/tools/TaskGetTool/prompt.ts`

### Description

```
Get a task by ID from the task list
```

### Prompt

```
Use this tool to retrieve a task by its ID from the task list.

## When to Use This Tool

- Need full description/context before starting work
- Understand dependencies (what it blocks / what blocks it)
- After being assigned a task, to get complete requirements

## Output

Returns full task details:
- subject
- description
- status: pending | in_progress | completed
- blocks
- blockedBy

## Tips

- Verify blockedBy is empty before beginning work
- Use TaskList for summary view of all tasks
```

---

## TaskListTool

**Source:** `src/tools/TaskListTool/prompt.ts`

### Description

```
List all tasks in the task list
```

### Prompt

```
Use this tool to list all tasks in the task list.

## When to Use This Tool

- See available tasks (pending, no owner, not blocked)
- Check overall project progress
- Find blocked tasks and resolve dependencies
- After completing a task, check newly unblocked work or claim next task
- Prefer working tasks in ID order (lowest first)

## Output

Returns summary for each task:
- id
- subject
- status
- owner
- blockedBy

Use TaskGet with a task ID to view full details.
```

### Teammate Workflow section (`isAgentSwarmsEnabled`)

When swarms are enabled, the prompt adds this workflow:

1. After completing current task, call `TaskList`
2. Find tasks with `pending`, no owner, empty `blockedBy`
3. Prefer lowest task ID first
4. Claim task with `TaskUpdate.owner`
5. If blocked, help unblock or notify team lead

---

## TaskUpdateTool

**Source:** `src/tools/TaskUpdateTool/prompt.ts`

### Description

```
Update a task in the task list
```

### Prompt

```
Use this tool to update a task in the task list.

## When to Use This Tool

Mark tasks as resolved:
- when work is completed
- when task is no longer needed/superseded
- always mark assigned tasks resolved when finished

ONLY mark completed when FULLY accomplished.
If blocked/errors, keep in_progress and create blocker tasks.

Delete tasks:
- set status to `deleted` to permanently remove task

Update details:
- requirements changed or clarified
- set dependencies

## Fields You Can Update

- status
- subject
- description
- activeForm
- owner
- metadata (merge; null deletes key)
- addBlocks
- addBlockedBy

## Status Workflow

pending -> in_progress -> completed
or deleted (permanent removal)

## Staleness

Read latest state with TaskGet before updating.

## Examples

{"taskId": "1", "status": "in_progress"}
{"taskId": "1", "status": "completed"}
{"taskId": "1", "status": "deleted"}
{"taskId": "1", "owner": "my-name"}
{"taskId": "2", "addBlockedBy": ["1"]}
```

---

## TaskStopTool

**Source:** `src/tools/TaskStopTool/prompt.ts`

### Description

```
- Stops a running background task by its ID
- Takes a task_id parameter identifying the task to stop
- Returns a success or failure status
- Use this tool when you need to terminate a long-running task
```

### Notes

This is a minimal description-only tool prompt (`TASK_STOP_TOOL_NAME = "TaskStop"`).
