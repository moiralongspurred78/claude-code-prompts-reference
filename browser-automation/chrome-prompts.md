# Chrome / Browser Automation Prompts

Primary browser automation prompts and skill hints.

---

## Base Chrome Prompt

**Source:** `src/utils/claudeInChrome/prompt.ts`

### `BASE_CHROME_PROMPT`

Core system prompt for `mcp__claude-in-chrome__*` tools:

- GIF recording guidance (`gif_creator`) with extra frames and meaningful filenames
- console log debugging guidance (`read_console_messages`, regex `pattern` usage)
- strict warning against triggering JS alert/confirm/prompt dialogs
- anti-loop/rabbit-hole stop conditions (ask user after repeated failures)
- startup requirement to call `tabs_context_mcp` first
- tab/session handling rules (refresh invalid tab IDs, avoid stale IDs)

---

## ToolSearch Instructions for Chrome

### `CHROME_TOOL_SEARCH_INSTRUCTIONS`

```
IMPORTANT: Before using any chrome browser tools, you MUST first load them using ToolSearch.

Chrome browser tools are MCP tools that require loading before use.
Before calling any mcp__claude-in-chrome__* tool:
1. Use ToolSearch with select:mcp__claude-in-chrome__<tool_name>
2. Then call the tool
```

Includes explicit tabs-context example.

---

## Skill Hints

### `CLAUDE_IN_CHROME_SKILL_HINT`

```
Browser Automation: Chrome browser tools are available via the "claude-in-chrome" skill.
CRITICAL: Before using any mcp__claude-in-chrome__* tools, invoke Skill(skill: "claude-in-chrome").
```

### `CLAUDE_IN_CHROME_SKILL_HINT_WITH_WEBBROWSER`

When built-in WebBrowser exists:

- steer dev-loop tasks to `WebBrowser`
- reserve `claude-in-chrome` for authenticated real-Chrome sessions (logged-in flows/OAuth/computer-use)

---

## Claude-in-Chrome Skill Prompt

**Source:** `src/skills/bundled/claudeInChrome.ts`

Skill `claude-in-chrome` wraps `BASE_CHROME_PROMPT` and appends activation text:

```
Now that this skill is invoked, you have access to Chrome browser automation tools...
IMPORTANT: Start by calling mcp__claude-in-chrome__tabs_context_mcp
```

- allowed tools: all `mcp__claude-in-chrome__${tool.name}` derived from browser tool registry
- enabled via `shouldAutoEnableClaudeInChrome()`
- optional task args appended under `## Task`
