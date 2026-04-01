# Skill: /simplify

**Source:** `src/skills/bundled/simplify.ts`

## Purpose

Runs a 3-agent parallel review over current diffs to improve:

1. code reuse,
2. code quality,
3. efficiency.

Then applies fixes directly.

## Registration Metadata

- name: `simplify`
- userInvocable: `true`
- description: review changed code for reuse/quality/efficiency and fix issues

## Prompt (`SIMPLIFY_PROMPT`)

### Phase 1: Identify Changes

- Run `git diff` (or `git diff HEAD` if staged changes)
- If no git changes, inspect recently modified files related to current session

### Phase 2: Launch Three Review Agents in Parallel

Single Agent-tool message with 3 concurrent agents, each given full diff:

1. **Code Reuse Review**
   - find existing helpers/utilities that could replace new code
   - flag duplicate functionality
   - flag inline logic that should use existing utilities

2. **Code Quality Review**
   - redundant state
   - parameter sprawl
   - copy-paste variations
   - leaky abstractions
   - stringly-typed patterns
   - unnecessary JSX nesting
   - unnecessary comments (keep non-obvious WHY only)

3. **Efficiency Review**
   - redundant work / duplicate reads/calls / N+1
   - missed concurrency
   - hot-path bloat
   - recurring no-op updates
   - TOCTOU existence checks
   - memory leaks / missing cleanup
   - overly broad operations

### Phase 3: Fix Issues

- wait for all 3 agents
- aggregate findings
- fix real issues, skip false positives briefly
- summarize what was fixed

## Extra Focus Support

If user passes args, appends:

```
## Additional Focus
<args>
```
