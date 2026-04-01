# Skill: /update-config

**Source:** `src/skills/bundled/updateConfig.ts`

## Purpose

Configuration-editing skill for Claude Code settings, with heavy focus on hook construction and safe settings merging.

## Core Prompt

### `UPDATE_CONFIG_PROMPT`

Defines:

- when hooks are required vs plain memory preferences
- mandatory read-before-write behavior
- ambiguity resolution via `AskUserQuestion`
- decision split: Config tool (simple settings) vs direct JSON edits (hooks, permissions, env, MCP, plugins)
- array merge guidance (preserve existing entries)

### Embedded docs in prompt

- `SETTINGS_EXAMPLES_DOCS`
- `HOOKS_DOCS`
- `HOOK_VERIFICATION_FLOW`

## Hook verification flow (`HOOK_VERIFICATION_FLOW`)

Stepwise operational checklist:

1. dedup check on target event+matcher
2. construct command for this project (safe stdin extraction)
3. pipe-test raw command with synthetic hook payload
4. write merged JSON
5. validate with `jq -e`
6. prove hook firing via trigger/sentinel
7. cleanup + user handoff

Also documents watcher caveat requiring `/hooks` reload or restart if proof fails despite valid config.
