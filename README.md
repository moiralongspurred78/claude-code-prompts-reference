# Claude Code — Complete AI Prompt Reference

Curated reference of prompt text used in Claude Code, extracted and organized into standalone docs.

---

## What this repo contains

This folder documents prompt logic across Claude Code in five groups:

- **System prompt** assembly and policy sections
- **Tool prompts** (`DESCRIPTION` / `PROMPT` and prompt builders)
- **Skill prompts** (bundled slash-command workflows)
- **Browser automation** prompt guidance
- **Command-specific prompts** (`/review`, `/ultraplan`)

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
│   └── claude-api.md
│
├── browser-automation/
│   └── chrome-prompts.md
│
├── companion/
│   └── buddy-prompt.md
│
└── commands/
     ├── review.md
     └── ultraplan.md
```

---

## Prompt architecture (high-level)

Claude Code prompt behavior is mostly split into three layers:

1. **Global system prompt** (`src/constants/prompts.ts`)
   - static/cached sections
   - dynamic environment/session sections
   - explicit system-prompt boundary handling

2. **Tool prompts** (`src/tools/*/prompt.ts`)
   - per-tool capability instructions
   - usage constraints and examples
   - feature-gated behavior branches

3. **Skill / command prompts** (`src/skills/bundled/*.ts`, `src/commands/*.ts`)
   - user-invocable workflow prompts (`/batch`, `/loop`, `/schedule`, etc.)
   - orchestration prompts (`/ultraplan`, `/ultrareview` path context)

---

## Coverage summary

### System prompt docs

- `main-system-prompt.md`
- `security-instruction.md`

### Tool prompt docs

- file I/O, shell, search
- agents / messaging / skills
- plan mode, todo/task APIs, team APIs
- cron scheduling, worktrees, MCP/deferred tools
- sleep/config/LSP/notebook/remote-trigger/brief

### Skill docs

- `/batch`, `/simplify`, `/debug`, `/stuck`
- `/remember`, `/skillify`, `/loop`, `/schedule`, `/claude-api`

### Extra prompt surfaces

- Chrome automation prompts and hints
- Companion intro prompt
- `/review` and `/ultraplan` command prompt notes

---

## How to use this reference

1. Start in **system-prompt/** for global behavior.
2. Use **tools/** to inspect exact per-tool instructions.
3. Use **skills/** for slash-command orchestration logic.
4. Check **commands/** for command-specific prompt entry points.

---

## Scope note

- This reference tracks prompt material from Claude Code source files.
- No external repository is used as a source of truth for this folder.
- If an external URL/name appears in quoted prompt text, it is preserved because it exists in source.
