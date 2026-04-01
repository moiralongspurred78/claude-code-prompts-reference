# Permission & Classifier Prompts

## Sources

- `src/utils/permissions/permissionExplainer.ts`
- `src/utils/permissions/yoloClassifier.ts`
- `src/cli/handlers/autoMode.ts`

---

## Permission explainer

### `SYSTEM_PROMPT` (`permissionExplainer.ts`)

```
Analyze shell commands and explain what they do, why you're running them, and potential risks.
```

Used with forced structured output tool `explain_command` returning:

- `explanation`
- `reasoning`
- `risk`
- `riskLevel` (`LOW|MEDIUM|HIGH`)

---

## Auto-mode critique prompt

### `CRITIQUE_SYSTEM_PROMPT` (`autoMode.ts`)

LLM reviewer prompt for custom auto-mode classifier rules (`allow`, `soft_deny`, `environment`):

- clarity
- completeness
- conflicts
- actionability

---

## Auto-mode classifier templates

### `BASE_PROMPT`

### `EXTERNAL_PERMISSIONS_TEMPLATE`

### `ANTHROPIC_PERMISSIONS_TEMPLATE`

Loaded in `yoloClassifier.ts` from bundled text assets:

- `auto_mode_system_prompt.txt`
- `permissions_external.txt`
- `permissions_anthropic.txt`

These templates are consumed to build effective classifier prompts and defaults (`buildDefaultExternalSystemPrompt`, `getDefaultExternalAutoModeRules`).

> Note: the referenced text asset files are bundled at build/runtime and are not present as standalone source files in this workspace snapshot.
