# Skill: /claude-api

**Source:** `src/skills/bundled/claudeApi.ts`

## Purpose

Provides in-context Claude API / SDK documentation and usage guidance, with automatic language detection and embedded references.

## Registration Metadata

- name: `claude-api`
- userInvocable: `true`
- allowedTools: `Read`, `Grep`, `Glob`, `WebFetch`
- description contains triggering rules:
  - trigger when project imports Anthropic SDKs (`anthropic`, `@anthropic-ai/sdk`, `claude_agent_sdk`)
  - do not trigger for unrelated SDKs/general ML tasks

## Language Detection

Detects project language from cwd file indicators:

- python (`.py`, `requirements.txt`, etc.)
- typescript (`.ts`, `.tsx`, `tsconfig.json`, `package.json`)
- java, go, ruby, csharp, php
- fallback unknown

## Prompt Construction

`buildPrompt(lang, args, content)`:

1. takes bundled `SKILL_PROMPT`
2. removes/avoids duplicate reading-guide section
3. injects `INLINE_READING_GUIDE`
4. embeds selected docs as `<doc path="...">...</doc>` blocks
   - language-specific docs + shared docs
   - if language unknown: include all docs, ask user to specify
5. preserves `## When to Use WebFetch` and subsequent sections
6. appends `## User Request` when args present

## Inline Reading Guide (`INLINE_READING_GUIDE`)

Includes quick mapping:

- single text tasks -> language `README.md`
- streaming/chat UI -> `streaming.md`
- compaction -> `README.md` section
- prompt caching -> `shared/prompt-caching.md`
- tool use/agents -> `shared/tool-use-concepts.md` + language docs
- batches, files API, agent SDK (Python/TS)
- error handling -> `shared/error-codes.md`
- live docs via `shared/live-sources.md`

## Content Processing

`processContent()`:

- strips HTML comments
- substitutes `{{var}}` placeholders from `SKILL_MODEL_VARS`

## Performance Notes

`claudeApiContent.js` (~247KB md strings) is lazily imported only when skill invoked.
