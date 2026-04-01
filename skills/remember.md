# Skill: /remember

**Source:** `src/skills/bundled/remember.ts`

## Purpose

Review memory layers and propose promotions/cleanup across auto-memory, `CLAUDE.md`, `CLAUDE.local.md`, and optional team memory. It does **not** apply changes automatically.

## Availability

- **Ant-only**
- enabled only when auto-memory is enabled (`isAutoMemoryEnabled()`)
- userInvocable: `true`

## Prompt (`SKILL_PROMPT`) Workflow

### Goal

```
Review user's memory landscape and produce a clear report of proposed changes,
grouped by action type. Do NOT apply changes — present proposals for user approval.
```

### Step 1: Gather all layers

- read `CLAUDE.md` and `CLAUDE.local.md` if present
- review auto-memory from system prompt
- note team memory sections if any

### Step 2: Classify auto-memory entries

Destination matrix:

- `CLAUDE.md`: project-wide conventions/instructions
- `CLAUDE.local.md`: user-specific Claude preferences
- Team memory: org-wide cross-repo knowledge
- Stay in auto-memory: temporary/uncertain notes

### Step 3: Cleanup opportunities

- duplicates
- outdated entries
- conflicts across layers

### Step 4: Present report

Grouped sections:

1. Promotions
2. Cleanup
3. Ambiguous
4. No action needed

## Rules

- present all proposals first
- no file modification without explicit approval
- do not create new files unless target missing
- ask for ambiguous destination decisions
