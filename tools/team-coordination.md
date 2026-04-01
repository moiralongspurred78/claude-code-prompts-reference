# Team Coordination Tools

Tools for creating and deleting multi-agent teams.

---

## TeamCreateTool

**Source:** `src/tools/TeamCreateTool/prompt.ts`

### Prompt

```
# TeamCreate

## When to Use

Use this tool proactively whenever:
- The user explicitly asks to use a team, swarm, or group of agents
- The user mentions wanting agents to work together, coordinate, or collaborate
- A task is complex enough that it would benefit from parallel work by multiple agents

When in doubt about whether a task warrants a team, prefer spawning a team.

## Choosing Agent Types for Teammates

When spawning teammates via the Agent tool, choose `subagent_type` based on tool needs:

- Read-only agents (Explore, Plan): research/search/planning only
- Full-capability agents (general-purpose): implementation changes
- Custom agents (`.claude/agents/`): follow their specific restrictions

Always review agent descriptions and available tools before selecting subagent_type.

Create a new team to coordinate multiple agents working on a project.
Teams have a 1:1 correspondence with task lists (Team = TaskList).

{
  "team_name": "my-project",
  "description": "Working on feature X"
}

This creates:
- `~/.claude/teams/{team-name}/config.json`
- `~/.claude/tasks/{team-name}/`

## Team Workflow

1. Create team with TeamCreate
2. Create tasks using Task tools
3. Spawn teammates with Agent (`team_name` + `name`)
4. Assign tasks with TaskUpdate.owner
5. Teammates complete tasks via TaskUpdate
6. Teammates go idle between turns (normal behavior)
7. Shutdown teammates via SendMessage `shutdown_request`

## Task Ownership

Tasks are assigned with TaskUpdate.owner. Any agent can set/change ownership.

## Automatic Message Delivery

Messages from teammates are delivered automatically:
- no inbox polling
- queued while you're mid-turn
- notification shown when messages are waiting

When reporting teammate messages, don't quote original message (already rendered).

## Teammate Idle State

Idle after each turn is normal:
- idle teammates can receive messages
- idle notifications are automatic
- do not treat idle as error
- peer DM summaries appear in idle notifications for visibility

## Discovering Team Members

Team config: `~/.claude/teams/{team-name}/config.json`
Contains `members` with:
- `name` (use this for messaging/task assignment)
- `agentId` (reference only)
- `agentType`

Always refer to teammates by NAME, never UUID.

## Task List Coordination

Shared task list path: `~/.claude/tasks/{team-name}/`

Teammates should:
1. Check TaskList periodically (especially after each task)
2. Claim unassigned/unblocked tasks, prefer lowest ID first
3. Create new tasks when additional work found
4. Mark done tasks completed, then check TaskList again
5. Coordinate via task list status
6. If all tasks blocked, notify team lead/help unblock

## IMPORTANT notes for communication

- Do not use terminal tools to view team activity; send messages
- Team cannot hear you unless you use SendMessage
- Do NOT send structured JSON status messages (`{"type":"idle"}` etc.)
- Use TaskUpdate for task completion
- For team agents, idle notifications are auto-sent to team lead
```
