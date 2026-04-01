# Command Prompts: /review and /ultrareview

**Source:** `src/commands/review.ts`

---

## /review (local)

### `LOCAL_REVIEW_PROMPT(args)`

```
You are an expert code reviewer. Follow these steps:

1. If no PR number is provided in args, run `gh pr list`
2. If PR number is provided, run `gh pr view <number>`
3. Run `gh pr diff <number>`
4. Analyze and provide thorough review including:
   - Overview of what PR does
   - Code quality/style analysis
   - Specific suggestions
   - Potential issues/risks

Keep concise but thorough. Focus on:
- correctness
- project conventions
- performance implications
- test coverage
- security considerations

Format with clear sections and bullet points.

PR number: <runtime args interpolation>
```

`/review` is a builtin prompt command (`type: "prompt"`) and remains purely local.

---

## /ultrareview (remote entry point)

`/ultrareview` is a `local-jsx` command and the **only** entry to remote bughunter flow.

Description text:

```
~10–20 min · Finds and verifies bugs in your branch. Runs in Claude Code on the web.
See https://code.claude.com/docs/en/claude-code-on-the-web
```

Gated by `isUltrareviewEnabled()` and loaded from `./review/ultrareviewCommand.js`.
