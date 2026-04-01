# Command Prompt: /ultraplan

**Source:** `src/commands/ultraplan.tsx`

## Overview

`/ultraplan` is a remote planning command that launches a CCR session, waits for approved exit from plan mode, and then routes execution target handling.

## Prompt Sources

### 1) Default instruction text (bundled)

`DEFAULT_INSTRUCTIONS` is loaded from:

- `../utils/ultraplan/prompt.txt` (bundled at build time)

Notes in code:

- wrapped in `<system-reminder>` style handling so UI hides scaffolding while model sees full text
- text intentionally avoids feature-name self-trigger phrasing in remote CLI keyword detection
- in tests/bundles, `.txt` may be imported as string or `{ default }`

### 2) Dev override (Ant-only)

`ULTRAPLAN_INSTRUCTIONS` can be overridden by env var:

- `ULTRAPLAN_PROMPT_FILE`

This is eager top-level file read and intended for internal dev use.

## Prompt Assembly

### `buildUltraplanPrompt(blurb: string, seedPlan?: string)`

Builds initial CCR user message:

- if `seedPlan` exists, prepends:
  - `Here is a draft plan to refine:`
  - seed plan body
- appends `ULTRAPLAN_INSTRUCTIONS`
- appends optional `blurb`

Result is joined with newline separators.

## Related constants

- `ULTRAPLAN_TIMEOUT_MS = 30 * 60 * 1000`
- `CCR_TERMS_URL = https://code.claude.com/docs/en/claude-code-on-the-web`

## Model selection

`getUltraplanModel()` reads GrowthBook key `tengu_ultraplan_model`, defaulting to first-party `opus46` config.

## Polling behavior

Detached poll waits for approved `ExitPlanMode` in remote session (`pollForApprovedExitPlanMode`) and logs analytics for awaiting input / approval / reject count / execution target.

---

## Missing local source note

In this workspace snapshot, `src/utils/ultraplan/prompt.txt` is not present as a standalone source file (bundled artifact path reference only). The command logic and prompt loading path above are still captured accurately from source.
