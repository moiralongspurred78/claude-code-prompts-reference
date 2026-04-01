# Miscellaneous Tools

Other prompt-bearing tools not grouped elsewhere.

---

## SleepTool

**Source:** `src/tools/SleepTool/prompt.ts`

### Description

```
Wait for a specified duration
```

### Prompt (`SLEEP_TOOL_PROMPT`)

```
Wait for a specified duration. The user can interrupt the sleep at any time.

Use this when the user tells you to sleep or rest, when you have nothing to do,
or when you're waiting for something.

You may receive <tick> prompts — these are periodic check-ins. Look for useful work
to do before sleeping.

You can call this concurrently with other tools — it won't interfere with them.

Prefer this over Bash(sleep ...) — it doesn't hold a shell process.

Each wake-up costs an API call, but the prompt cache expires after 5 minutes of inactivity
— balance accordingly.
```

---

## BriefTool (`SendUserMessage`)

**Source:** `src/tools/BriefTool/prompt.ts`

### Names

- `BRIEF_TOOL_NAME = "SendUserMessage"`
- `LEGACY_BRIEF_TOOL_NAME = "Brief"`

### Description

```
Send a message to the user
```

### Prompt (`BRIEF_TOOL_PROMPT`)

```
Send a message the user will read. Text outside this tool is visible in the detail view,
but most won't open it — the answer lives here.

`message` supports markdown. `attachments` takes file paths for images, diffs, logs.

`status` labels intent:
- normal: replying to what they just asked
- proactive: you're initiating (scheduled task finished, blocker surfaced, need input)
Set it honestly; downstream routing uses it.
```

### Proactive Section (`BRIEF_PROACTIVE_SECTION`)

A large guidance block describing messaging cadence and visibility contract:

- real answer must go through `SendUserMessage`
- ack quickly before long work (`On it — checking ...`)
- send meaningful checkpoints, not filler
- keep messages tight and second-person

---

## ConfigTool

**Source:** `src/tools/ConfigTool/prompt.ts`

### Description

```
Get or set Claude Code configuration settings.
```

### Generated Prompt Shape

`generatePrompt()` builds the tool prompt dynamically from `SUPPORTED_SETTINGS`:

```
Get or set Claude Code configuration settings.

View or change Claude Code settings. Use when user requests configuration changes,
asks about current settings, or when adjusting a setting would benefit them.

## Usage
- Get current value: omit `value`
- Set new value: include `value`

## Configurable settings list
### Global Settings (stored in ~/.claude.json)
### Project Settings (stored in settings.json)

## Model
- model - Override default model. Available options (dynamic from getModelOptions)

## Examples
{"setting":"theme"}
{"setting":"theme","value":"dark"}
{"setting":"editorMode","value":"vim"}
{"setting":"verbose","value":true}
{"setting":"model","value":"opus"}
{"setting":"permissions.defaultMode","value":"plan"}
```

Voice settings are hidden when voice mode is build-enabled but runtime gate is off.

---

## LSPTool

**Source:** `src/tools/LSPTool/prompt.ts`

### Description

```
Interact with Language Server Protocol (LSP) servers to get code intelligence features.

Supported operations:
- goToDefinition
- findReferences
- hover
- documentSymbol
- workspaceSymbol
- goToImplementation
- prepareCallHierarchy
- incomingCalls
- outgoingCalls

All operations require:
- filePath
- line (1-based)
- character (1-based)

Note: LSP servers must be configured for file type.
```

---

## NotebookEditTool

**Source:** `src/tools/NotebookEditTool/prompt.ts`

### Description

```
Replace the contents of a specific cell in a Jupyter notebook.
```

### Prompt

```
Completely replaces the contents of a specific cell in a Jupyter notebook (.ipynb)
with new source. notebook_path must be absolute path. cell_number is 0-indexed.
Use edit_mode=insert to add a new cell at index cell_number.
Use edit_mode=delete to delete the cell at index cell_number.
```

---

## RemoteTriggerTool

**Source:** `src/tools/RemoteTriggerTool/prompt.ts`

### Description

```
Manage scheduled remote Claude Code agents (triggers) via the claude.ai CCR API.
Auth is handled in-process — token never reaches shell.
```

### Prompt

```
Call the claude.ai remote-trigger API. Use this instead of curl — OAuth token is added
in-process and never exposed.

Actions:
- list: GET /v1/code/triggers
- get: GET /v1/code/triggers/{trigger_id}
- create: POST /v1/code/triggers (requires body)
- update: POST /v1/code/triggers/{trigger_id} (partial update)
- run: POST /v1/code/triggers/{trigger_id}/run

Response is raw JSON from API.
```
