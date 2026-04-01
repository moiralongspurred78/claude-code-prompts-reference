# Shell Tools — Bash, PowerShell

> **Sources**: `src/tools/BashTool/prompt.ts`, `src/tools/PowerShellTool/prompt.ts`

---

## Bash Tool (`BASH_TOOL_NAME = 'Bash'`)

### Full Prompt (`getSimplePrompt`)

```
Executes a given bash command and returns its output.

The working directory persists between commands, but shell state does not.
The shell environment is initialized from the user's profile (bash or zsh).

IMPORTANT: Avoid using this tool to run find, grep, cat, head, tail, sed,
awk, or echo commands, unless explicitly instructed or after you have verified
that a dedicated tool cannot accomplish your task. Instead, use the appropriate
dedicated tool as this will provide a much better experience for the user:

 - File search: Use Glob (NOT find or ls)
 - Content search: Use Grep (NOT grep or rg)
 - Read files: Use Read (NOT cat/head/tail)
 - Edit files: Use Edit (NOT sed/awk)
 - Write files: Use Write (NOT echo >/cat <<EOF)
 - Communication: Output text directly (NOT echo/printf)

While the Bash tool can do similar things, it's better to use the built-in
tools as they provide a better user experience and make it easier to review
tool calls and give permission.

# Instructions

 - If your command will create new directories or files, first use this tool
   to run `ls` to verify the parent directory exists and is the correct
   location.
 - Always quote file paths that contain spaces with double quotes in your
   command (e.g., cd "path with spaces/file.txt")
 - Try to maintain your current working directory throughout the session by
   using absolute paths and avoiding usage of `cd`. You may use `cd` if the
   User explicitly requests it.
 - You may specify an optional timeout in milliseconds (up to 600000ms / 10
   minutes). By default, your command will timeout after 120000ms (2 minutes).
 - You can use the `run_in_background` parameter to run the command in the
   background. Only use this if you don't need the result immediately and are
   OK being notified when the command completes later. You do not need to
   check the output right away — you'll be notified when it finishes. You do
   not need to use '&' at the end of the command when using this parameter.
 - When issuing multiple commands:
   - If the commands are independent and can run in parallel, make multiple
     Bash tool calls in a single message.
   - If the commands depend on each other and must run sequentially, use a
     single Bash call with '&&' to chain them together.
   - Use ';' only when you need to run commands sequentially but don't care
     if earlier commands fail.
   - DO NOT use newlines to separate commands (newlines are ok in quoted strings).
 - For git commands:
   - Prefer to create a new commit rather than amending an existing commit.
   - Before running destructive operations (e.g., git reset --hard, git push
     --force, git checkout --), consider whether there is a safer alternative
     that achieves the same goal. Only use destructive operations when they
     are truly the best approach.
   - Never skip hooks (--no-verify) or bypass signing (--no-gpg-sign,
     -c commit.gpgsign=false) unless the user has explicitly asked for it.
     If a hook fails, investigate and fix the underlying issue.
 - Avoid unnecessary `sleep` commands:
   - Do not sleep between commands that can run immediately — just run them.
   - If your command is long running and you would like to be notified when it
     finishes — use `run_in_background`. No sleep needed.
   - Do not retry failing commands in a sleep loop — diagnose the root cause.
   - If waiting for a background task you started with `run_in_background`,
     you will be notified when it completes — do not poll.
   - If you must poll an external process, use a check command (e.g.
     `gh run view`) rather than sleeping first.
   - If you must sleep, keep the duration short (1-5 seconds) to avoid
     blocking the user.
```

---

### Git Commit Instructions (External Build)

```
# Committing changes with git

Only create commits when requested by the user. If unclear, ask first.

Git Safety Protocol:
- NEVER update the git config
- NEVER run destructive git commands (push --force, reset --hard, checkout .,
  restore ., clean -f, branch -D) unless the user explicitly requests these actions.
- NEVER skip hooks (--no-verify, --no-gpg-sign, etc) unless the user explicitly requests it
- NEVER run force push to main/master, warn the user if they request it
- CRITICAL: Always create NEW commits rather than amending, unless the user
  explicitly requests a git amend. When a pre-commit hook fails, the commit did
  NOT happen — so --amend would modify the PREVIOUS commit, which may result in
  destroying work or losing previous changes. Instead, after hook failure, fix the
  issue, re-stage, and create a NEW commit
- When staging files, prefer adding specific files by name rather than using
  "git add -A" or "git add .", which can accidentally include sensitive files
  (.env, credentials) or large binaries
- NEVER commit changes unless the user explicitly asks you to.

Steps:
1. Run in parallel: git status (no -uall flag!), git diff, git log
2. Analyze all staged changes and draft a commit message:
   - Summarize the nature of the changes (new feature, bug fix, refactoring, etc.)
   - Do not commit files that likely contain secrets (.env, credentials.json, etc)
   - Draft a concise (1-2 sentences) commit message focusing on the "why"
3. Run in parallel:
   - Add relevant untracked files to staging area
   - Create the commit using HEREDOC format:
     git commit -m "$(cat <<'EOF'
     Commit message here.
     EOF
     )"
   - Run git status after the commit to verify success.
4. If the commit fails due to pre-commit hook: fix the issue and create a NEW commit

Important notes:
- NEVER run additional commands to read or explore code
- NEVER use the TodoWrite or Agent tools during commit
- DO NOT push to the remote repository unless explicitly asked

# Creating pull requests

IMPORTANT: When creating a pull request, follow these steps:
1. Run in parallel: git status, git diff, check if branch tracks remote, git log
2. Analyze all changes and draft PR title and summary:
   - Keep the PR title short (under 70 characters)
   - Use the description/body for details
3. Run in parallel: create branch if needed, push with -u flag if needed, create PR:

gh pr create --title "the pr title" --body "$(cat <<'EOF'
## Summary
<1-3 bullet points>

## Test plan
[Bulleted markdown checklist of TODOs for testing the pull request...]
EOF
)"

- Return the PR URL when done
```

---

### Sandbox Section (`getSimpleSandboxSection`)

When sandbox mode is enabled:

```
## Command sandbox

By default, your command will be run in a sandbox. This sandbox controls which
directories and network hosts commands may access or modify without an explicit
override.

The sandbox has the following restrictions:
Filesystem: {"read": {...}, "write": {"allowOnly": [...]}}
Network: {"allowedHosts": [...]}

 - You should always default to running commands within the sandbox. Do NOT
   attempt to set `dangerouslyDisableSandbox: true` unless:
   - The user *explicitly* asks you to bypass sandbox
   - A specific command just failed and you see evidence of sandbox restrictions
     causing the failure.
 - Evidence of sandbox-caused failures includes:
   - "Operation not permitted" errors for file/network operations
   - Access denied to specific paths outside allowed directories
   - Network connection failures to non-whitelisted hosts
   - Unix socket connection errors
 - When you see evidence of sandbox-caused failure:
   - Immediately retry with `dangerouslyDisableSandbox: true`
   - Briefly explain what sandbox restriction likely caused the failure.
   - This will prompt the user for permission
 - For temporary files, always use the `$TMPDIR` environment variable. TMPDIR
   is automatically set to the correct sandbox-writable directory. Do NOT use
   `/tmp` directly.
```

---

## PowerShell Tool (`POWERSHELL_TOOL_NAME = 'PowerShell'`)

### Full Prompt (`getPrompt`)

```
Executes a given PowerShell command with optional timeout. Working directory
persists between commands; shell state (variables, functions) does not.

IMPORTANT: This tool is for terminal operations via PowerShell: git, npm,
docker, and PS cmdlets. DO NOT use it for file operations (reading, writing,
editing, searching, finding files) — use the specialized tools for this instead.
```

#### Edition-specific guidance:

**Windows PowerShell 5.1** (`powershell.exe`):

```
- Pipeline chain operators `&&` and `||` are NOT available — use `A; if ($?) { B }`
- Ternary `?:`, null-coalescing `??`, and null-conditional `?.` are NOT available
- Avoid `2>&1` on native executables (wraps stderr in ErrorRecord, sets $? false)
- Default file encoding is UTF-16 LE (with BOM). Pass `-Encoding utf8` when needed.
- `ConvertFrom-Json` returns PSCustomObject, not hashtable. `-AsHashtable` not available.
```

**PowerShell 7+** (`pwsh`):

```
- Pipeline chain operators `&&` and `||` ARE available — prefer `cmd1 && cmd2`
- Ternary `$cond ? $a : $b`, null-coalescing `??`, and null-conditional `?.` available
- Default file encoding is UTF-8 without BOM.
```

**Unknown edition** (conservative 5.1 assumptions):

```
- Do NOT use `&&`, `||`, ternary `?:`, `??`, or `?.`
- To chain commands conditionally: `A; if ($?) { B }`. Unconditionally: `A; B`.
```

```
PowerShell Syntax Notes:
 - Variables use $ prefix: $myVar = "value"
 - Escape character is backtick (`), not backslash
 - Use Verb-Noun cmdlet naming: Get-ChildItem, Set-Location, New-Item, Remove-Item
 - Common aliases: ls (Get-ChildItem), cd (Set-Location), cat (Get-Content), rm (Remove-Item)
 - Registry access: HKLM:\SOFTWARE\..., HKCU:\... (NOT raw HKEY_LOCAL_MACHINE\...)
 - Environment variables: read with $env:NAME, set with $env:NAME = "value"
 - Call native exe with spaces in path: & "C:\Program Files\App\app.exe" arg1

Interactive and blocking commands (will hang — runs with -NonInteractive):
 - NEVER use Read-Host, Get-Credential, Out-GridView, $Host.UI.PromptForChoice, or pause
 - Add -Confirm:$false for destructive cmdlets. Use -Force for read-only/hidden items.

Passing multiline strings to native executables (use here-strings):
<example>
git commit -m @'
Commit message here.
Second line with $literal dollar signs.
'@
</example>
 - Use @'...'@ (single-quoted, literal) not @"..."@ (double-quoted, interpolated)
   unless you need variable expansion
 - For arguments containing -, @, or other special chars, use stop-parsing token:
   git log --% --format=%H

Usage notes:
 - Avoid using PowerShell for file operations — use Read, Edit, Write, Glob, Grep
 - For independent commands: make multiple PowerShell tool calls in parallel
 - For sequential commands: chain with appropriate chaining syntax for your edition
 - For git commands: prefer new commits over amending; never skip hooks
 - Avoid unnecessary Start-Sleep commands
```
