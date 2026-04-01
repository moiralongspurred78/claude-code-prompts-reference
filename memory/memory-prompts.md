# Memory System Prompts

## Sources

- `src/memdir/memdir.ts`
- `src/memdir/teamMemPrompts.ts`
- `src/memdir/findRelevantMemories.ts`
- `src/services/extractMemories/prompts.ts`
- `src/services/SessionMemory/prompts.ts`
- `src/services/autoDream/consolidationPrompt.ts`
- `src/services/MagicDocs/prompts.ts`

---

## Core memory prompt builders

### `buildMemoryPrompt()` / `buildMemoryLines()`

Defines persistent memory policy for personal memory with typed memory guidance, save/update behavior, and retrieval/searching section.

### `buildCombinedMemoryPrompt()`

Team + personal memory variant with explicit scope rules:

- private memory path
- team memory path
- type-driven scope selection
- separate index rules

### `buildAssistantDailyLogPrompt()`

Assistant-mode append-only daily-log prompt (`logs/YYYY/MM/YYYY-MM-DD.md`) with nightly distillation model.

---

## Memory retrieval selection

### `SELECT_MEMORIES_SYSTEM_PROMPT`

Model prompt for selecting up to 5 relevant memory files from filename + description manifest.
Key constraint: avoid selecting tool API docs for tools already actively used, except warnings/gotchas.

---

## Background extraction prompts

### `buildExtractAutoOnlyPrompt()`

### `buildExtractCombinedPrompt()`

Both prompts:

- run as memory extraction subagent
- restricted toolset and turn-budget strategy
- require semantic dedup against existing files
- include memory format and index update instructions
- include what-not-to-save constraints

---

## Session memory prompts

### `DEFAULT_SESSION_MEMORY_TEMPLATE`

Canonical sectioned session-notes template (Current State, Files/Functions, Errors & Corrections, Worklog, etc.).

### default updater prompt (`getDefaultUpdatePrompt()`)

Strict edit-only instructions for updating notes file:

- preserve headers and italic template lines exactly
- update content only beneath template lines
- parallel `Edit` calls only
- section/total size budget reminders

Custom prompt/template overrides supported via:

- `~/.claude/session-memory/config/template.md`
- `~/.claude/session-memory/config/prompt.md`

---

## Dream consolidation

### `buildConsolidationPrompt()`

Nightly “dream” prompt to consolidate memory/log/transcript signal into durable topic memories and prune index entries.

---

## Magic Docs updater prompt

### `buildMagicDocsUpdatePrompt()` template

Instructions for doc-updater subagent:

- update docs in-place to current truth (not changelog style)
- preserve `# MAGIC DOC: <title>` header
- high-signal architecture/entry-point documentation only
- avoid verbose low-level walkthroughs
