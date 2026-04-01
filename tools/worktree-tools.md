# Worktree Tools

Tools for entering and exiting isolated worktree sessions.

---

## EnterWorktreeTool

**Source:** `src/tools/EnterWorktreeTool/prompt.ts`

### Prompt

```
Use this tool ONLY when the user explicitly asks to work in a worktree.
This tool creates an isolated git worktree and switches the current session into it.

## When to Use
- User explicitly says "worktree" (start/work/create/use a worktree)

## When NOT to Use
- User asks to create/switch branches (use git commands instead)
- User asks to fix bug/build feature (use normal git workflow unless worktree is explicit)
- Never use unless user explicitly mentions "worktree"

## Requirements
- Must be in a git repository OR have WorktreeCreate/WorktreeRemove hooks in settings.json
- Must not already be in a worktree

## Behavior
- In git repo: creates new git worktree under `.claude/worktrees/` with new branch from HEAD
- Outside git repo: delegates to WorktreeCreate/WorktreeRemove hooks for VCS-agnostic isolation
- Switches session cwd to new worktree
- Use ExitWorktree to leave mid-session (keep/remove)
- On session exit, user is prompted to keep/remove if still in worktree

## Parameters
- `name` (optional): worktree name; random if omitted
```

---

## ExitWorktreeTool

**Source:** `src/tools/ExitWorktreeTool/prompt.ts`

### Prompt

```
Exit a worktree session created by EnterWorktree and return the session to the original
working directory.

## Scope
This tool ONLY operates on worktrees created by EnterWorktree in this session.
It will NOT touch:
- worktrees created manually with `git worktree add`
- worktrees from previous sessions
- current directory if EnterWorktree was never called

If called outside an EnterWorktree session, it is a no-op.

## When to Use
- User explicitly asks to exit/leave/go back from worktree
- Do NOT call proactively

## Parameters
- `action` (required): "keep" or "remove"
  - keep: preserve worktree directory + branch
  - remove: delete worktree directory + branch
- `discard_changes` (optional, default false): only meaningful with action="remove".
  If worktree has uncommitted files or commits not on original branch, removal is refused
  unless discard_changes=true.

## Behavior
- Restores original working directory
- Clears CWD-dependent caches (system prompt sections, memory files, plans directory)
- If tmux attached to worktree:
  - killed on remove
  - kept running on keep (name returned for reattach)
- After exit, EnterWorktree can create a fresh worktree
```
