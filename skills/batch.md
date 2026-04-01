# Skill: /batch

**Source:** `src/skills/bundled/batch.ts`

## Purpose

Large-scale parallel orchestration skill. It plans a sweeping change, decomposes into 5–30 independent units, and runs one isolated worktree agent per unit (background), each expected to open a PR.

## Registration Metadata

- name: `batch`
- userInvocable: `true`
- disableModelInvocation: `true`
- argumentHint: `<instruction>`
- requires git repo (`getIsGit()` guard)

## Prompt Core

`buildPrompt(instruction)` returns a long orchestration prompt with 3 phases:

1. **Phase 1 — Research and Plan (Plan Mode)**
   - call `EnterPlanMode`
   - use subagents for scope discovery
   - decompose work into `MIN_AGENTS=5` to `MAX_AGENTS=30` self-contained units
   - find concrete e2e verification recipe (or ask user via `AskUserQuestion`)
   - write plan file and call `ExitPlanMode`

2. **Phase 2 — Spawn Workers (after approval)**
   - spawn one background agent per unit
   - **must** use `isolation: "worktree"` and `run_in_background: true`
   - include complete, self-contained unit prompt and shared worker instructions

3. **Phase 3 — Track Progress**
   - render status table
   - parse each agent result line `PR: <url>`
   - update final done/failed counts

## Shared Worker Instructions

```
After you finish implementing the change:
1. Simplify — invoke Skill(skill: "simplify")
2. Run unit tests and fix failures
3. Run end-to-end test recipe (or skip per recipe)
4. Commit, push, and create PR with gh pr create
5. End with a single line: PR: <url>
   (or PR: none — <reason>)
```

## Guardrail Messages

- Not a git repo: emits explicit error text about worktree/PR requirement
- Missing instruction: emits usage examples (`/batch migrate ...`, etc.)
