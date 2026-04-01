# Service-Level Prompt Surfaces

## Sources

- `src/services/compact/prompt.ts`
- `src/services/awaySummary.ts`
- `src/services/toolUseSummary/toolUseSummaryGenerator.ts`
- `src/services/PromptSuggestion/promptSuggestion.ts`
- `src/utils/agenticSessionSearch.ts`
- `src/utils/sessionTitle.ts`

---

## Compaction prompts

### `NO_TOOLS_PREAMBLE` + `NO_TOOLS_TRAILER`

Hard requirement: text-only compact output, no tool calls.

### `BASE_COMPACT_PROMPT`

Full conversation compact schema with sections for:

- request intent
- technical concepts
- files/snippets
- errors/fixes
- user messages
- pending/current work
- next step alignment

### `PARTIAL_COMPACT_PROMPT`

Recent-window-only compact for partial compaction.

### `PARTIAL_COMPACT_UP_TO_PROMPT`

Prefix-summary mode for “up_to” partial compact where newer messages follow summary.

---

## Away summary prompt

### `buildAwaySummaryPrompt()`

1–3 sentence recap prompt used when user returns after being away:

- start with high-level task
- include concrete next step
- avoid status/commit recap tone

---

## Tool-use summary prompt

### `TOOL_USE_SUMMARY_SYSTEM_PROMPT`

Generates short mobile-row labels for completed tool batches.
Constraints: terse, commit-subject style, strongest noun + past-tense verb.

---

## Prompt suggestion mode

### `SUGGESTION_PROMPT`

Predicts what the user would naturally type next.
Strong constraints:

- user-voice only
- no evaluative chatter/questions/new ideas
- 2–12 words or silence

---

## Agentic session search prompt

### `SESSION_SEARCH_SYSTEM_PROMPT`

Semantic retrieval over past sessions using metadata, tags, branch, summary, transcript excerpts.
Instruction bias: be inclusive; return ordered relevant indices as JSON.

---

## Session title generation prompt

### `SESSION_TITLE_PROMPT`

Generate concise sentence-case session title (3–7 words), JSON schema output `{ "title": ... }`.
Includes explicit good/bad examples and casing rules.
