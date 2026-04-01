# Planning & User Interaction Tools

Tools for entering/exiting plan mode, asking the user questions, and managing to-do lists.

---

## EnterPlanModeTool

**Source:** `src/tools/EnterPlanModeTool/prompt.ts`

Two variants: **external** (shown to non-Ant users) and **ant** (shown to Ant employees). The Ant variant is more conservative — biased toward just starting work.

### What Happens in Plan Mode (shared section)

```
## What Happens in Plan Mode

In plan mode, you'll:
1. Thoroughly explore the codebase using Glob, Grep, and Read tools
2. Understand existing patterns and architecture
3. Design an implementation approach
4. Present your plan to the user for approval
5. Use AskUserQuestion if you need to clarify approaches
6. Exit plan mode with ExitPlanMode when ready to implement
```

> **Note:** When the interview phase feature flag (`isPlanModeInterviewPhaseEnabled`) is on, this section is omitted — detailed workflow instructions arrive via the plan_mode attachment instead.

### External Variant (Prompt)

```
Use this tool proactively when you're about to start a non-trivial implementation task.
Getting user sign-off on your approach before writing code prevents wasted effort and
ensures alignment. This tool transitions you into plan mode where you can explore the
codebase and design an implementation approach for user approval.

## When to Use This Tool

**Prefer using EnterPlanMode** for implementation tasks unless they're simple.
Use it when ANY of these conditions apply:

1. **New Feature Implementation**: Adding meaningful new functionality
2. **Multiple Valid Approaches**: The task can be solved in several different ways
3. **Code Modifications**: Changes that affect existing behavior or structure
4. **Architectural Decisions**: The task requires choosing between patterns or technologies
5. **Multi-File Changes**: The task will likely touch more than 2-3 files
6. **Unclear Requirements**: You need to explore before understanding the full scope
7. **User Preferences Matter**: If you would use AskUserQuestion to clarify the approach,
   use EnterPlanMode instead — plan mode lets you explore first, then present options with context

## When NOT to Use This Tool

Only skip EnterPlanMode for simple tasks:
- Single-line or few-line fixes (typos, obvious bugs, small tweaks)
- Adding a single function with clear requirements
- Tasks where the user has given very specific, detailed instructions
- Pure research/exploration tasks (use the Agent tool with explore agent instead)

## Important Notes

- This tool REQUIRES user approval — they must consent to entering plan mode
- If unsure whether to use it, err on the side of planning
```

### Ant Variant (Prompt)

```
Use this tool when a task has genuine ambiguity about the right approach and getting user
input before coding would prevent significant rework.

## When to Use This Tool

Plan mode is valuable when the implementation approach is genuinely unclear:

1. **Significant Architectural Ambiguity**: Multiple reasonable approaches exist
2. **Unclear Requirements**: You need to explore and clarify before making progress
3. **High-Impact Restructuring**: Major restructuring where getting buy-in reduces risk

## When NOT to Use This Tool

Skip plan mode when you can reasonably infer the right approach:
- The task is straightforward even if it touches multiple files
- The user's request is specific enough that the implementation path is clear
- Adding a feature with an obvious implementation pattern
- Bug fixes where the fix is clear once you understand the bug
- Research/exploration tasks (use Agent tool instead)
- The user says something like "can we work on X" — just get started

When in doubt, prefer starting work and using AskUserQuestion for specific questions
over entering a full planning phase.

## Important Notes

- This tool REQUIRES user approval
```

---

## ExitPlanModeTool

**Source:** `src/tools/ExitPlanModeTool/prompt.ts`

### Prompt (`EXIT_PLAN_MODE_V2_TOOL_PROMPT`)

```
Use this tool when you are in plan mode and have finished writing your plan to the plan
file and are ready for user approval.

## How This Tool Works
- You should have already written your plan to the plan file specified in the plan mode
  system message
- This tool does NOT take the plan content as a parameter — it will read the plan from
  the file you wrote
- This tool simply signals that you're done planning and ready for the user to review
  and approve
- The user will see the contents of your plan file when they review it

## When to Use This Tool
IMPORTANT: Only use this tool when the task requires planning the implementation steps
of a task that requires writing code. For research tasks where you're gathering
information, searching files, reading files, or trying to understand the codebase —
do NOT use this tool.

## Before Using This Tool
Ensure your plan is complete and unambiguous:
- If you have unresolved questions about requirements or approach, use AskUserQuestion
  first (in earlier phases)
- Once your plan is finalized, use THIS tool to request approval

**Important:** Do NOT use AskUserQuestion to ask "Is this plan okay?" or "Should I
proceed?" — that's exactly what THIS tool does. ExitPlanMode inherently requests user
approval of your plan.

## Examples

1. Task: "Search for and understand the implementation of vim mode" — do NOT use, you
   are not planning implementation steps
2. Task: "Help me implement yank mode for vim" — USE after finishing implementation
   planning
3. Task: "Add a new feature to handle user authentication" — if unsure about auth method,
   use AskUserQuestion first, then use ExitPlanMode after clarifying
```

---

## AskUserQuestionTool

**Source:** `src/tools/AskUserQuestionTool/prompt.ts`

### Description

```
Asks the user multiple choice questions to gather information, clarify ambiguity,
understand preferences, make decisions or offer them choices.
```

### Prompt (`ASK_USER_QUESTION_TOOL_PROMPT`)

```
Use this tool when you need to ask the user questions during execution. This allows you to:
1. Gather user preferences or requirements
2. Clarify ambiguous instructions
3. Get decisions on implementation choices as you work
4. Offer choices to the user about what direction to take.

Usage notes:
- Users will always be able to select "Other" to provide custom text input
- Use multiSelect: true to allow multiple answers to be selected for a question
- If you recommend a specific option, make that the first option in the list and add
  "(Recommended)" at the end of the label

Plan mode note: In plan mode, use this tool to clarify requirements or choose between
approaches BEFORE finalizing your plan. Do NOT use this tool to ask "Is my plan ready?"
or "Should I proceed?" — use ExitPlanMode for plan approval. IMPORTANT: Do not reference
"the plan" in your questions because the user cannot see the plan in the UI until you
call ExitPlanMode.
```

### Preview Feature Prompt (Markdown variant)

```
Preview feature:
Use the optional `preview` field on options when presenting concrete artifacts that users
need to visually compare:
- ASCII mockups of UI layouts or components
- Code snippets showing different implementations
- Diagram variations
- Configuration examples

Preview content is rendered as markdown in a monospace box. Multi-line text with newlines
is supported. When any option has a preview, the UI switches to a side-by-side layout
with a vertical option list on the left and preview on the right. Do not use previews
for simple preference questions where labels and descriptions suffice.
Note: previews are only supported for single-select questions (not multiSelect).
```

---

## TodoWriteTool

**Source:** `src/tools/TodoWriteTool/prompt.ts`

### Description

```
Update the todo list for the current session. To be used proactively and often to track
progress and pending tasks. Make sure that at least one task is in_progress at all times.
Always provide both content (imperative) and activeForm (present continuous) for each task.
```

### Full Prompt (185 lines)

```
Use this tool to create and manage a structured task list for your current coding session.
This helps you track progress, organize complex tasks, and demonstrate thoroughness to
the user. It also helps the user understand the progress of the task and overall progress
of their requests.

## When to Use This Tool

Use this tool proactively in these scenarios:

1. Complex multi-step tasks — When a task requires 3 or more distinct steps or actions
2. Non-trivial and complex tasks — Tasks that require careful planning or multiple operations
3. User explicitly requests todo list — When the user directly asks you to use the todo list
4. User provides multiple tasks — When users provide a list of things to be done
5. After receiving new instructions — Immediately capture user requirements as todos
6. When you start working on a task — Mark it as in_progress BEFORE beginning work.
   Ideally you should only have one todo as in_progress at a time
7. After completing a task — Mark it as completed and add any new follow-up tasks

## When NOT to Use This Tool

Skip using this tool when:
1. There is only a single, straightforward task
2. The task is trivial and tracking it provides no organizational benefit
3. The task can be completed in less than 3 trivial steps
4. The task is purely conversational or informational

## Task States and Management

1. **Task States**:
   - pending: Task not yet started
   - in_progress: Currently working on (limit to ONE task at a time)
   - completed: Task finished successfully

   **IMPORTANT**: Task descriptions must have two forms:
   - content: The imperative form (e.g., "Run tests", "Build the project")
   - activeForm: The present continuous form (e.g., "Running tests", "Building the project")

2. **Task Management**:
   - Update task status in real-time as you work
   - Mark tasks complete IMMEDIATELY after finishing (don't batch completions)
   - Exactly ONE task must be in_progress at any time
   - Complete current tasks before starting new ones
   - Remove tasks that are no longer relevant from the list entirely

3. **Task Completion Requirements**:
   - ONLY mark a task as completed when you have FULLY accomplished it
   - If you encounter errors, blockers, or cannot finish, keep the task as in_progress
   - When blocked, create a new task describing what needs to be resolved
   - Never mark a task as completed if:
     - Tests are failing
     - Implementation is partial
     - You encountered unresolved errors
     - You couldn't find necessary files or dependencies

4. **Task Breakdown**:
   - Create specific, actionable items
   - Break complex tasks into smaller, manageable steps
   - Use clear, descriptive task names
   - Always provide both content and activeForm

When in doubt, use this tool. Being proactive with task management demonstrates
attentiveness and ensures you complete all requirements successfully.
```

> **Full prompt includes rich examples** (dark mode toggle, function rename, multiple features, performance optimization) and counter-examples (hello world, git status explanation) illustrating when to use vs. skip the todo list.
