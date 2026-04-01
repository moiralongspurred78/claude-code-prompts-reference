# Skill: /stuck

**Source:** `src/skills/bundled/stuck.ts`

## Purpose

Diagnose frozen/stuck/slow Claude Code sessions on the local machine and report confirmed incidents to Slack channel `#claude-code-feedback`.

## Availability

- **Ant-only** (`process.env.USER_TYPE === 'ant'`)
- userInvocable: `true`

## Prompt (`STUCK_PROMPT`) Highlights

### Detection heuristics

- sustained CPU >= 90%
- process state `D` (uninterruptible sleep)
- process state `T` (stopped)
- process state `Z` (zombie)
- RSS >= 4GB
- stuck child process (`git`, `node`, shell)

### Investigation steps

1. List candidate processes via `ps` (claude/cli)
2. Inspect suspicious ones:
   - child processes (`pgrep -lP <pid>`)
   - resample high CPU after 1–2s
   - inspect child command line
   - check debug log tail (`~/.claude/debug/<session-id>.txt`) when inferable
3. Optional stack sample (`sample <pid> 3` on macOS)

### Reporting protocol

- **Only report if actual stuck/slow issue found**
- Post to Slack channel ID `C07VBSHV7EV`
- Two-message structure:
  1. top-level short symptom summary
  2. threaded detailed diagnostics (with `thread_ts`)
- If Slack MCP unavailable, provide copy-paste message for user

### Constraints

- diagnostic-only; do not kill/signal processes
- if args provided (PID/symptom), focus there first
