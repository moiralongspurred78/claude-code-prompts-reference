# Prompt Source Audit (Whole-Codebase)

This file maps prompt-bearing source files to documentation in this repo.

## Legend
- ✅ documented
- ⚠️ bundled/indirect surface (asset not present as standalone source file)

---

## Core system prompt

- ✅ `src/constants/prompts.ts` -> `system-prompt/main-system-prompt.md`
- ✅ `src/constants/cyberRiskInstruction.ts` -> `system-prompt/security-instruction.md`

---

## Tool prompts (`src/tools/**/prompt.ts`)

- ✅ Agent/SendMessage/Skill -> `tools/agent-coordination.md`
- ✅ AskUserQuestion/EnterPlan/ExitPlan/TodoWrite -> `tools/planning-tools.md`
- ✅ FileRead/FileWrite/FileEdit -> `tools/file-tools.md`
- ✅ Bash/PowerShell -> `tools/shell-tools.md`
- ✅ Glob/Grep/WebSearch/WebFetch -> `tools/search-tools.md`
- ✅ TaskCreate/TaskGet/TaskList/TaskUpdate/TaskStop -> `tools/task-management.md`
- ✅ TeamCreate/TeamDelete -> `tools/team-coordination.md`
- ✅ ScheduleCron (`CronCreate/CronDelete/CronList`) -> `tools/scheduling.md`
- ✅ EnterWorktree/ExitWorktree -> `tools/worktree-tools.md`
- ✅ MCPTool/ListMcpResources/ReadMcpResource/ToolSearch -> `tools/mcp-tools.md`
- ✅ Sleep/Config/LSP/NotebookEdit/RemoteTrigger/Brief -> `tools/misc-tools.md`

---

## Bundled skills

- ✅ `skills/bundled/batch.ts` -> `skills/batch.md`
- ✅ `skills/bundled/simplify.ts` -> `skills/simplify.md`
- ✅ `skills/bundled/debug.ts` -> `skills/debug.md`
- ✅ `skills/bundled/stuck.ts` -> `skills/stuck.md`
- ✅ `skills/bundled/remember.ts` -> `skills/remember.md`
- ✅ `skills/bundled/skillify.ts` -> `skills/skillify.md`
- ✅ `skills/bundled/loop.ts` -> `skills/loop.md`
- ✅ `skills/bundled/scheduleRemoteAgents.ts` -> `skills/schedule.md`
- ✅ `skills/bundled/claudeApi.ts` -> `skills/claude-api.md`
- ✅ `skills/bundled/updateConfig.ts` -> `skills/update-config.md`

---

## Command prompts

- ✅ `src/commands/review.ts` -> `commands/review.md`
- ✅ `src/commands/ultraplan.tsx` -> `commands/ultraplan.md`
- ✅ `src/commands/init.ts` -> `commands/init-and-insights.md`
- ✅ `src/commands/insights.ts` -> `commands/init-and-insights.md`
- ✅ `src/cli/handlers/autoMode.ts` -> `commands/auto-mode.md`

---

## Memory subsystem prompts

- ✅ `src/memdir/findRelevantMemories.ts` -> `memory/memory-prompts.md`
- ✅ `src/memdir/memdir.ts` -> `memory/memory-prompts.md`
- ✅ `src/memdir/teamMemPrompts.ts` -> `memory/memory-prompts.md`
- ✅ `src/services/extractMemories/prompts.ts` -> `memory/memory-prompts.md`
- ✅ `src/services/SessionMemory/prompts.ts` -> `memory/memory-prompts.md`
- ✅ `src/services/autoDream/consolidationPrompt.ts` -> `memory/memory-prompts.md`
- ✅ `src/services/MagicDocs/prompts.ts` -> `memory/memory-prompts.md`

---

## Service/runtime prompts

- ✅ `src/services/compact/prompt.ts` -> `services/session-and-meta-prompts.md`
- ✅ `src/services/awaySummary.ts` -> `services/session-and-meta-prompts.md`
- ✅ `src/services/toolUseSummary/toolUseSummaryGenerator.ts` -> `services/session-and-meta-prompts.md`
- ✅ `src/services/PromptSuggestion/promptSuggestion.ts` -> `services/session-and-meta-prompts.md`
- ✅ `src/utils/agenticSessionSearch.ts` -> `services/session-and-meta-prompts.md`
- ✅ `src/utils/sessionTitle.ts` -> `services/session-and-meta-prompts.md`

---

## Internal-agent prompts

- ✅ `src/tools/AgentTool/built-in/statuslineSetup.ts` -> `internal-agents/internal-agent-prompts.md`
- ✅ `src/tools/AgentTool/built-in/verificationAgent.ts` -> `internal-agents/internal-agent-prompts.md`
- ✅ `src/components/agents/generateAgent.ts` -> `internal-agents/internal-agent-prompts.md`
- ✅ `src/services/AgentSummary/agentSummary.ts` -> `internal-agents/internal-agent-prompts.md`
- ✅ `src/utils/swarm/teammatePromptAddendum.ts` -> `internal-agents/internal-agent-prompts.md`
- ✅ `src/cli/print.ts` (`SHUTDOWN_TEAM_PROMPT`) -> `internal-agents/internal-agent-prompts.md`

---

## Permission/classifier prompts

- ✅ `src/utils/permissions/permissionExplainer.ts` -> `permissions/permission-and-classifier-prompts.md`
- ✅ `src/utils/permissions/yoloClassifier.ts` -> `permissions/permission-and-classifier-prompts.md`
- ✅ `src/cli/handlers/autoMode.ts` -> `permissions/permission-and-classifier-prompts.md`
- ⚠️ `auto_mode_system_prompt.txt` / `permissions_external.txt` / `permissions_anthropic.txt` referenced by `yoloClassifier.ts` are bundled assets (not present as standalone source files in this workspace snapshot)

---

## Interface/UX prompts

- ✅ `src/constants/outputStyles.ts` -> `interface/output-and-teleport-prompts.md`
- ✅ `src/utils/teleport.tsx` -> `interface/output-and-teleport-prompts.md`
- ✅ `src/utils/claudeInChrome/prompt.ts` -> `browser-automation/chrome-prompts.md`
- ✅ `src/buddy/prompt.ts` -> `companion/buddy-prompt.md`

---

## Notes

- This audit targets prompt text/constants/builders (not every string literal in code).
- Source-of-truth remains the TypeScript files listed above.
