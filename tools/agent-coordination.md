# Agent Coordination Tools

Tools for spawning, messaging, and managing agents and skills.

---

## AgentTool

**Source:** `src/tools/AgentTool/prompt.ts`

### Core Description

```
Launch a new agent to handle complex, multi-step tasks autonomously.

The Agent tool launches specialized agents (subprocesses) that autonomously handle complex tasks. Each agent type has specific capabilities and tools available to it.
```

### Fork Mode (when `FORK_SUBAGENT` feature flag is on)

When fork mode is enabled, omitting `subagent_type` **forks yourself** — the fork inherits your full conversation context.

### When to Fork (fork-enabled builds)

```
When to fork vs. delegate
- Fork yourself when the task needs your full context and you'd otherwise have to
  copy-paste a large portion of the conversation into a prompt. A fork is you,
  running in parallel, with the same conversation history.
- Delegate to a named agent type when the task is self-contained and you can brief
  the agent completely in a short prompt. Delegation is cheaper and faster.

Don't peek at your fork's work while it's running — merge its result when it
reports back. Don't race two forks on the same files.

Writing the fork prompt
- Be brief — the fork already has context. A sentence or two is usually enough.
- Tell it what to do and what to return.
- Don't re-explain the entire task.
```

### Writing the Prompt Section

```
Writing the prompt
Brief the agent like a smart colleague who just walked into the room. Don't
re-explain context the agent will discover itself. Don't delegate understanding —
tell it what you already know and what decision you need.
```

### Fork Examples

```
Example 1 — ship-audit fork:
  You: "Can you audit the codebase for any uses of deprecated 'ship' function?"
  Fork: "Audit the codebase for deprecated 'ship' function calls. Return a list of files and line numbers."

Example 2 — mid-wait status:
  While waiting for a build to finish, fork to check test coverage stats.

Example 3 — migration-review fresh agent:
  "Review the migration files in db/migrate/ for correctness. Return any issues found."
```

### Non-Fork Examples (always available)

```
Example — test-runner agent:
  {"subagent_type": "test-runner", "description": "run full test suite", "prompt": "Run the full test suite and return a summary of results."}

Example — greeting-responder agent:
  {"subagent_type": "general-purpose", "description": "respond to greeting", "prompt": "The user said hi. Respond warmly."}
```

### When NOT to Use the Agent Tool (non-fork builds)

```
When NOT to use the Agent tool:
- If you want to read a specific file path, use the Read tool or Glob/find via Bash
  instead, to find the match more quickly
- If you are searching for a specific class definition like "class Foo", use Grep/Glob
  instead, to find the match more quickly
- If you are searching for code within a specific file or set of 2-3 files, use
  the Read tool instead, to find the match more quickly
- Other tasks that are not related to the agent descriptions above
```

### Usage Notes (non-coordinator, non-fork mode)

```
Usage notes:
- Always include a short description (3-5 words) summarizing what the agent will do
- Launch multiple agents concurrently whenever possible (Pro accounts: single message
  with multiple tool uses)
- When the agent is done, it will return a single message back to you. The result
  returned by the agent is not visible to the user — send a text message with a concise
  summary of the result.
- To continue a previously spawned agent, use SendMessage with the agent's ID or name
  as the `to` field. Each fresh Agent invocation starts fresh — provide a complete
  task description.
- The agent's outputs should generally be trusted
- Clearly tell the agent whether you expect it to write code or just to do research
  (search, file reads, web fetches, etc.), since it is not aware of the user's intent
- If the agent description mentions that it should be used proactively, try your best
  to use it without the user having to ask for it first.
- If the user specifies "in parallel", you MUST send a single message with multiple
  Agent tool use content blocks.
- You can optionally set `isolation: "worktree"` to run the agent in a temporary git
  worktree (cleaned up automatically if no changes; worktree path and branch returned
  if changes are made).
- [Ant only] You can set `isolation: "remote"` to run the agent in a remote CCR
  environment (always background, long-running tasks).
- Background agents: use `run_in_background: true` for genuinely independent work.
  Foreground (default): when you need the agent's results before proceeding.
```

### Coordinator Mode

When `isCoordinator` is true, the model receives only the slim shared prompt (agent list + basic description). Full usage notes, examples, and when-not-to-use guidance are already in the coordinator's system prompt.

---

## SendMessageTool

**Source:** `src/tools/SendMessageTool/prompt.ts`

### Prompt

```
# SendMessage

Send a message to another agent.

{"to": "researcher", "summary": "assign task 1", "message": "start on task #1"}

| `to` | |
|---|---|
| `"researcher"` | Teammate by name |
| `"*"` | Broadcast to all teammates — expensive (linear in team size), use only when everyone genuinely needs it |
| `"uds:/tmp/cc-socks/1234.sock"` | Local Claude session's socket (same machine; use `ListPeers`) |   [UDS_INBOX gate]
| `"bridge:session_01AbCd..."` | Remote Control peer session (cross-machine; use `ListPeers`) |   [UDS_INBOX gate]

Your plain text output is NOT visible to other agents — to communicate, you MUST call this tool. Messages from teammates are delivered automatically; you don't check an inbox. Refer to teammates by name, never by UUID. When relaying, don't quote the original — it's already rendered to the user.

## Cross-session [UDS_INBOX gate]

Use `ListPeers` to discover targets, then:
{"to": "uds:/tmp/cc-socks/1234.sock", "message": "check if tests pass over there"}
{"to": "bridge:session_01AbCd...", "message": "what branch are you on?"}

A listed peer is alive and will process your message — no "busy" state; messages enqueue
and drain at the receiver's next tool round. Your message arrives wrapped as
`<cross-session-message from="...">`. To reply, copy the `from` attribute as your `to`.

## Protocol responses (legacy)

If you receive a JSON message with `type: "shutdown_request"` or `type: "plan_approval_request"`, respond with the matching `_response` type — echo the `request_id`, set `approve` true/false:

{"to": "team-lead", "message": {"type": "shutdown_response", "request_id": "...", "approve": true}}
{"to": "researcher", "message": {"type": "plan_approval_response", "request_id": "...", "approve": false, "feedback": "add error handling"}}

Approving shutdown terminates your process. Rejecting plan sends the teammate back to revise. Don't originate `shutdown_request` unless asked. Don't send structured JSON status messages — use TaskUpdate.
```

---

## SkillTool

**Source:** `src/tools/SkillTool/prompt.ts`

### Prompt

```
Execute a skill within the main conversation

When users ask you to perform tasks, check if any of the available skills match.
Skills provide specialized capabilities and domain knowledge.

When users reference a "slash command" or "/<something>" (e.g., "/commit", "/review-pr"),
they are referring to a skill. Use this tool to invoke it.

How to invoke:
- Use this tool with the skill name and optional arguments
- Examples:
  - `skill: "pdf"` - invoke the pdf skill
  - `skill: "commit", args: "-m 'Fix bug'"` - invoke with arguments
  - `skill: "review-pr", args: "123"` - invoke with arguments
  - `skill: "ms-office-suite:pdf"` - invoke using fully qualified name

Important:
- Available skills are listed in system-reminder messages in the conversation
- When a skill matches the user's request, this is a BLOCKING REQUIREMENT: invoke
  the relevant Skill tool BEFORE generating any other response about the task
- NEVER mention a skill without actually calling this tool
- Do not invoke a skill that is already running
- Do not use this tool for built-in CLI commands (like /help, /clear, etc.)
- If you see a <command_name> tag in the current conversation turn, the skill has
  ALREADY been loaded — follow the instructions directly instead of calling this tool again
```

### Budget Management

The skill listing is subject to a character budget: 1% of the context window in characters (default 8,000 chars fallback). Bundled skills always get full descriptions. Non-bundled skill descriptions are truncated proportionally if over budget.

---

## Feature Flags

| Flag                        | Effect                                                                                          |
| --------------------------- | ----------------------------------------------------------------------------------------------- |
| `FORK_SUBAGENT`             | Enables fork-yourself semantics for AgentTool (omitting `subagent_type` forks the conversation) |
| `UDS_INBOX`                 | Enables cross-session messaging via Unix domain sockets and bridge sessions                     |
| `EXPERIMENTAL_SKILL_SEARCH` | Controls deferred tool delta behavior for SkillTool listing                                     |
