# Interface & UX Prompt Surfaces

## Sources

- `src/constants/outputStyles.ts`
- `src/utils/teleport.tsx`

---

## Output style prompts

### `EXPLANATORY_FEATURE_PROMPT`

Shared educational wrapper injected into built-in output styles.

### `OUTPUT_STYLE_CONFIG.Explanatory.prompt`

Style-mode system prompt emphasizing educational implementation insights.

### `OUTPUT_STYLE_CONFIG.Learning.prompt`

Interactive teaching mode prompt with:

- explicit “Learn by Doing” request format
- TODO(human) insertion workflow
- collaboration constraints before proceeding

---

## Teleport title/branch prompt

### `SESSION_TITLE_AND_BRANCH_PROMPT`

Prompt used to generate CCR session title + branch name from description.
Constraints:

- concise title (≤ ~6 words, sentence case)
- branch prefixed with `claude/`, lowercase, dash-separated
- JSON output with `title` and `branch`
