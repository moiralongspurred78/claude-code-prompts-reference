# Skill: /debug

**Source:** `src/skills/bundled/debug.ts`

## Purpose

Session debugging skill that inspects Claude Code debug logs and explains issues with actionable next steps.

## Registration Metadata

- name: `debug`
- allowedTools: `Read`, `Grep`, `Glob`
- argumentHint: `[issue description]`
- disableModelInvocation: `true`
- userInvocable: `true`
- external users: enables debug logging before analysis

## Runtime Behavior

- Reads tail of debug log only (`TAIL_READ_BYTES = 64KB`)
- Shows last `DEFAULT_DEBUG_LINES_READ = 20` lines
- Handles missing file (`ENOENT`) gracefully

## Just-Enabled Section

If debug logging was off, prompt includes:

```
## Debug Logging Just Enabled

Debug logging was OFF for this session until now.
Nothing prior to this /debug invocation was captured.

Tell user logging is now active at <path>, ask them to reproduce,
then re-read the log. If they can't reproduce, restart with `claude --debug`.
```

## Prompt Structure

`# Debug Skill` + sections:

- Session Debug Log path
- Tail output
- Issue Description (args or fallback instruction)
- Settings file locations (user/project/local)
- Instructions:
  1. review issue description
  2. scan for `[ERROR]` and `[WARN]`
  3. optionally launch `claude-code-guide` subagent
  4. explain findings plainly
  5. suggest concrete fixes
