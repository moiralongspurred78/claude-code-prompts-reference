# Skill: /skillify

**Source:** `src/skills/bundled/skillify.ts`

## Purpose

Converts a completed session workflow into a reusable `SKILL.md` through a structured interview.

## Availability

- **Ant-only**
- userInvocable: `true`
- disableModelInvocation: `true`
- argumentHint: `[description of the process you want to capture]`

## Allowed Tools

- `Read`, `Write`, `Edit`, `Glob`, `Grep`, `AskUserQuestion`, `Bash(mkdir:*)`

## Prompt (`SKILLIFY_PROMPT`) Phases

### Step 1: Analyze session

Analyze:

- repeatable process
- inputs/parameters
- ordered steps
- success artifacts/criteria per step
- user corrections/steering
- needed tools/permissions/agents

### Step 2: Interview user (AskUserQuestion-only)

4 rounds:

1. high-level confirmation (name/description/goals)
2. step list, arguments, context (`inline` vs `fork`), save location
3. per-step deep-dive (artifacts, success criteria, checkpoints, constraints)
4. final invocation triggers and gotchas

Rules:

- **all** questions via `AskUserQuestion`
- no custom "Needs tweaking" option (use built-in Other)
- stop once enough info collected; don't over-ask simple workflows

### Step 3: Write `SKILL.md`

Defines required format:

- frontmatter (`name`, `description`, `allowed-tools`, `when_to_use`, optional args/context)
- sections (`Inputs`, `Goal`, `Steps`)
- per-step `Success criteria` required
- optional annotations: Execution, Artifacts, Human checkpoint, Rules

### Step 4: Confirm and Save

Before writing file:

- output full `SKILL.md` as yaml code block for review
- ask concise confirmation question via `AskUserQuestion`

After writing:

- report save path
- show invocation `/skill-name [arguments]`
- mention direct file editing for refinement
