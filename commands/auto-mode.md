# Command Prompt Surface: `auto-mode` critique

## Source

- `src/cli/handlers/autoMode.ts`

---

## `CRITIQUE_SYSTEM_PROMPT`

Used by `claude auto-mode critique`.

Purpose:

- Review user-defined auto-mode classifier rules for quality and safety.
- Evaluate each rule section (`allow`, `soft_deny`, `environment`) on:
  1. clarity
  2. completeness
  3. conflicts
  4. actionability

Behavior notes:

- The command builds the effective classifier prompt and sends both default classifier context and user custom rules to the reviewer model.
- If no custom rules are configured, no critique prompt is sent.

Related prompt surfaces:

- `src/utils/permissions/yoloClassifier.ts` (classifier templates)
- `src/cli/handlers/autoMode.ts` (critique reviewer prompt)
