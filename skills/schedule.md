# Skill: /schedule

**Source:** `src/skills/bundled/scheduleRemoteAgents.ts`

## Purpose

Create, update, list, and run **remote Claude Code agents** (CCR triggers) via `RemoteTrigger` API.

## Registration Metadata

- name: `schedule`
- userInvocable: `true`
- allowedTools: `RemoteTrigger`, `AskUserQuestion`
- isEnabled requires:
  - GrowthBook gate `tengu_surreal_dali`
  - policy `allow_remote_sessions`

## Preconditions / Runtime Checks

- Requires claude.ai OAuth token (not API account)
- Fetches remote environments; if none, attempts to auto-create default cloud environment
- Performs soft setup checks (non-blocking):
  - repo detection / github access checks
  - connector presence
  - may add GitHub setup reminders

## Prompt (`buildPrompt`) Core Sections

### First Step

- If no args: first action must be a single `AskUserQuestion` with exact action-selection question
- If args provided: skip initial question and route directly by intent

### RemoteTrigger usage contract

- load via `ToolSearch select:RemoteTrigger`
- actions: `list`, `get`, `create`, `update`, `run`
- delete unsupported (redirect to `https://claude.ai/code/scheduled`)

### Create body schema

Provides full JSON template for trigger creation with:

- `name`
- `cron_expression`
- `enabled`
- `job_config.ccr` including
  - `environment_id`
  - `session_context.model` (default `claude-sonnet-4-6`)
  - git sources
  - `allowed_tools`
  - events payload containing user prompt and generated lowercase v4 UUID

### MCP connectors section

- Enumerates connected claude.ai proxy connectors with sanitized names
- Instructs model to infer needed services from user intent (e.g., Datadog + Slack)
- Warns/directs user if required connectors missing

### Environments section

- Lists available environments (`name`, `id`, `kind`)
- Uses `environment_id` in trigger config
- Notes when default environment was auto-created

### Cron + timezone handling

- User timezone read from runtime
- cron expressions are UTC
- requires explicit conversion confirmation for local-time requests
- minimum interval is 1 hour

### Workflow blocks

- CREATE:
  1. understand goal and remote constraints
  2. craft strong self-contained prompt
  3. set schedule with timezone conversion
  4. choose model (default sonnet 4.6)
  5. validate connectors and repo access
  6. review/confirm full config
  7. create trigger and output direct trigger URL
- UPDATE: list -> choose -> diff -> confirm -> update
- LIST: show readable fields (name, schedule, enabled, next run, repos)
- RUN NOW: choose trigger -> run -> confirm

### Important notes

- remote agents cannot access local machine state
- normalize GitHub URL formats to full HTTPS (no `.git`)
- prompt quality is primary success factor (remote starts with zero context)
- deletion via web UI only
