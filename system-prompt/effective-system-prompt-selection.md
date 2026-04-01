# Effective System Prompt Selection

## Source

- `src/utils/systemPrompt.ts`

---

## Purpose

This module determines which system prompt actually gets sent for a turn.

It defines `buildEffectiveSystemPrompt(...)` and enforces precedence/combination rules between:

- override prompt
- coordinator prompt
- main-thread agent prompt
- custom CLI prompt
- default prompt
- appended prompt suffix

---

## Selection order

`buildEffectiveSystemPrompt` applies this priority:

1. `overrideSystemPrompt` (hard replace)
2. Coordinator system prompt (`COORDINATOR_MODE` gate + env + no main-thread agent)
3. Agent system prompt (`mainThreadAgentDefinition`)
   - in proactive mode: append agent instructions to default prompt
   - otherwise: agent prompt replaces default prompt
4. `customSystemPrompt`
5. `defaultSystemPrompt`

`appendSystemPrompt` is appended in non-override paths.

---

## Notable prompt behavior

- In coordinator mode, it lazy-loads `getCoordinatorSystemPrompt()` and bypasses default prompt.
- For built-in agents, `getSystemPrompt(...)` may depend on tool context.
- In proactive mode, agent instructions are wrapped as:

```text
# Custom Agent Instructions
<agentSystemPrompt>
```

and appended after the default proactive prompt.

---

## Side effect

When main-thread agent memory is configured, this module logs `tengu_agent_memory_loaded` analytics metadata.
