# Command Prompts: /init and /insights

## Sources

- `src/commands/init.ts`
- `src/commands/insights.ts`

---

## `/init`

### Legacy prompt: `OLD_INIT_PROMPT`

Purpose: create/upgrade `CLAUDE.md` by scanning repo commands, architecture, and instruction files.

Core instructions include:

- include build/lint/test commands and architecture overview
- avoid generic filler guidance
- include key parts from `.cursor`, Copilot, README, etc.
- preserve fixed header block:

```md
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
```

### Current prompt: `NEW_INIT_PROMPT`

A full multi-phase workflow:

1. Ask setup choices with `AskUserQuestion` (project/personal CLAUDE files; skills/hooks selection)
2. Explore codebase with subagent
3. Fill gaps via user interview
4. Write `CLAUDE.md`
5. Write `CLAUDE.local.md`
6. Suggest/create skills
7. Suggest additional optimizations (gh CLI, linting, hooks)
8. Summarize and next steps

Notable behavior: proposal UI must use `AskUserQuestion.preview` and short option labels.

---

## `/insights`

### `FACET_EXTRACTION_PROMPT`

Used to extract structured session facets with explicit rubric for:

- goal categories (user-requested only)
- user satisfaction signals
- friction categories

### `SUMMARIZE_CHUNK_PROMPT`

Used for long transcripts before facet extraction:

- summarize user request
- Claude actions (tools/files)
- friction/issues
- outcome
- concise 3–5 sentence chunks

### JSON extraction prompt block

`extractFacetsFromAPI()` appends strict JSON schema requirements and accepted enums for outcome/helpfulness/session type.

These prompts power usage-data facet generation and analysis caching.
