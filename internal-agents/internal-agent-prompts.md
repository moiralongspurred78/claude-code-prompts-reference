# Internal Agent Prompts

## Sources

- `src/tools/AgentTool/built-in/statuslineSetup.ts`
- `src/tools/AgentTool/built-in/verificationAgent.ts`
- `src/components/agents/generateAgent.ts`
- `src/services/AgentSummary/agentSummary.ts`
- `src/utils/swarm/teammatePromptAddendum.ts`
- `src/cli/print.ts`

---

## Built-in: `statusline-setup`

### `STATUSLINE_SYSTEM_PROMPT`

Specialized setup agent that:

- reads shell rc files for `PS1`
- translates escapes to shell command forms
- updates `~/.claude/settings.json` `statusLine` command
- preserves existing settings and symlink target handling
- includes required reminder to keep using `statusline-setup` for future status line changes

---

## Built-in: `verification`

### `VERIFICATION_SYSTEM_PROMPT`

Adversarial verification agent prompt.
Core properties:

- verification-over-affirmation stance
- strict “do not modify project” constraints
- change-type-specific verification strategy (frontend/backend/cli/mobile/db/etc.)
- required command/output/result evidence format
- mandatory final parser line:
  - `VERDICT: PASS`
  - `VERDICT: FAIL`
  - `VERDICT: PARTIAL`

---

## Agent creation prompt

### `AGENT_CREATION_SYSTEM_PROMPT`

Prompt used to generate new custom agent specs (`identifier`, `whenToUse`, `systemPrompt` JSON object).
Includes:

- intent extraction
- persona design
- instruction architecture
- output schema requirements
- example guidance for proactive usage

### `AGENT_MEMORY_INSTRUCTIONS`

Conditionally appended when auto-memory is enabled; asks generated agents to maintain domain-specific memory updates.

---

## Agent progress summarization prompt

### `buildSummaryPrompt(previousSummary)`

Produces ultra-short 3–5 word present-tense action summaries for subagent progress updates.
Explicit good/bad examples included.

---

## Teammate addendum prompt

### `TEAMMATE_SYSTEM_PROMPT_ADDENDUM`

Extra system text for team agents:

- text responses are invisible to team
- communication must use `SendMessage`
- team lead centric coordination model

---

## Headless team shutdown reminder

### `SHUTDOWN_TEAM_PROMPT`

`<system-reminder>` block used in non-interactive mode enforcing graceful team shutdown before final user response.
