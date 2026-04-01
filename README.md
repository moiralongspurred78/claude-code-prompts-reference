# Claude Code — Complete AI Prompt Reference

Curated reference of prompt text used in Claude Code, extracted from source and organized into standalone markdown docs.

---

## What this repo contains

This repository documents prompt logic across Claude Code across all major prompt surfaces:

- **System prompt** assembly and policy sections
- **Tool prompts** (`DESCRIPTION` / `PROMPT` and prompt builders)
- **Skill prompts** (bundled slash-command workflows)
- **Browser automation** prompt guidance
- **Command-specific prompts** (`/review`, `/ultraplan`)
- **Additional command prompts** (`/init`, `/insights`)
- **Memory system prompts** (selection, extraction, session memory, consolidation)
- **Service-level prompts** (compact, summaries, suggestions, search/title)
- **Internal agent prompts** (verification/statusline/teammate/agent generation)
- **Permission/classifier prompts** (permission explainer, auto-mode classifier surfaces)
- **Interface prompts** (output styles, teleport title/branch)

---

## Quick navigation

### System prompt

- [main-system-prompt.md](system-prompt/main-system-prompt.md)
- [security-instruction.md](system-prompt/security-instruction.md)

### Tools

- [file-tools.md](tools/file-tools.md)
- [shell-tools.md](tools/shell-tools.md)
- [search-tools.md](tools/search-tools.md)
- [agent-coordination.md](tools/agent-coordination.md)
- [planning-tools.md](tools/planning-tools.md)
- [task-management.md](tools/task-management.md)
- [team-coordination.md](tools/team-coordination.md)
- [scheduling.md](tools/scheduling.md)
- [worktree-tools.md](tools/worktree-tools.md)
- [mcp-tools.md](tools/mcp-tools.md)
- [misc-tools.md](tools/misc-tools.md)

### Skills

- [batch.md](skills/batch.md)
- [simplify.md](skills/simplify.md)
- [debug.md](skills/debug.md)
- [stuck.md](skills/stuck.md)
- [remember.md](skills/remember.md)
- [skillify.md](skills/skillify.md)
- [loop.md](skills/loop.md)
- [schedule.md](skills/schedule.md)
- [claude-api.md](skills/claude-api.md)
- [update-config.md](skills/update-config.md)

### Command prompt surfaces

- [review.md](commands/review.md)
- [ultraplan.md](commands/ultraplan.md)
- [init-and-insights.md](commands/init-and-insights.md)

### Memory prompts

- [memory-prompts.md](memory/memory-prompts.md)

### Service prompts

- [session-and-meta-prompts.md](services/session-and-meta-prompts.md)

### Internal agent prompts

- [internal-agent-prompts.md](internal-agents/internal-agent-prompts.md)

### Permission/classifier prompts

- [permission-and-classifier-prompts.md](permissions/permission-and-classifier-prompts.md)

### Interface prompts

- [output-and-teleport-prompts.md](interface/output-and-teleport-prompts.md)

### Other prompt surfaces

- [chrome-prompts.md](browser-automation/chrome-prompts.md)
- [buddy-prompt.md](companion/buddy-prompt.md)

---

## Repository layout

```
claude-code-system-promtps/
├── README.md
├── .gitignore
│
├── system-prompt/
│   ├── main-system-prompt.md
│   └── security-instruction.md
│
├── tools/
│   ├── file-tools.md
│   ├── shell-tools.md
│   ├── search-tools.md
│   ├── agent-coordination.md
│   ├── planning-tools.md
│   ├── task-management.md
│   ├── team-coordination.md
│   ├── scheduling.md
│   ├── worktree-tools.md
│   ├── mcp-tools.md
│   └── misc-tools.md
│
├── skills/
│   ├── batch.md
│   ├── simplify.md
│   ├── debug.md
│   ├── stuck.md
│   ├── remember.md
│   ├── skillify.md
│   ├── loop.md
│   ├── schedule.md
│   ├── claude-api.md
│   └── update-config.md
│
├── browser-automation/
│   └── chrome-prompts.md
│
├── companion/
│   └── buddy-prompt.md
│
├── commands/
│   ├── review.md
│   ├── ultraplan.md
│   └── init-and-insights.md
│
├── memory/
│   └── memory-prompts.md
│
├── services/
│   └── session-and-meta-prompts.md
│
├── internal-agents/
│   └── internal-agent-prompts.md
│
├── permissions/
│   └── permission-and-classifier-prompts.md
│
└── interface/
    └── output-and-teleport-prompts.md
```

---

## Prompt architecture (high-level)

Claude Code prompt behavior is split into three primary layers, plus supporting prompt surfaces:

1. **Global system prompt** (`src/constants/prompts.ts`)
2. **Tool prompts** (`src/tools/*/prompt.ts`)
3. **Skill / command prompts** (`src/skills/bundled/*.ts`, `src/commands/*.ts`)

Supporting prompt surfaces include memory extraction/selection prompts, compaction/suggestion prompts, classifier prompts, and internal agent prompts.

---

## Coverage summary

- Core system prompt and security policy
- Full tool prompt set used in the main runtime
- Bundled skills and command entry prompts (including `/update-config`)
- Memory subsystem prompts (selection, extraction, session notes, consolidation)
- Background/service prompts (compact, away-summary, tool-use-summary, suggestions, semantic search, title generation)
- Internal agent prompts and teammate addenda
- Permission/classifier prompt surfaces
- UI/interface prompt surfaces (output styles, teleport)

---

## How to use this reference

1. Start in **system-prompt/** for global behavior.
2. Use **tools/** for callable tool instructions.
3. Use **skills/** and **commands/** for workflow prompts.
4. Use **memory/** and **services/** for background model prompt behavior.
5. Use **internal-agents/**, **permissions/**, and **interface/** for internal/runtime-specific prompt surfaces.

---

## Scope note

- This reference tracks prompt material from Claude Code source files.
- No external repository is used as a source of truth for this folder.
- If an external URL/name appears in quoted prompt text, it is preserved only because it exists in source.
