# File Tools — Read, Write, Edit

> **Sources**: `src/tools/FileReadTool/prompt.ts`, `src/tools/FileWriteTool/prompt.ts`, `src/tools/FileEditTool/prompt.ts`

---

## Read Tool (`FILE_READ_TOOL_NAME = 'Read'`)

### Description

```
Read a file from the local filesystem.
```

### Full Prompt (`renderPromptTemplate`)

```
Reads a file from the local filesystem. You can access any file directly by
using this tool. Assume this tool is able to read all files on the machine.
If the User provides a path to a file assume that path is valid. It is okay
to read a file that does not exist; an error will be returned.

Usage:
- The file_path parameter must be an absolute path, not a relative path
- By default, it reads up to 2000 lines starting from the beginning of the file
- You can optionally specify a line offset and limit (especially handy for
  long files), but it's recommended to read the whole file by not providing
  these parameters
- Results are returned using cat -n format, with line numbers starting at 1
- This tool allows Claude Code to read images (eg PNG, JPG, etc). When reading
  an image file the contents are presented visually as Claude Code is a
  multimodal LLM.
- This tool can read PDF files (.pdf). For large PDFs (more than 10 pages),
  you MUST provide the pages parameter to read specific page ranges
  (e.g., pages: "1-5"). Reading a large PDF without the pages parameter will
  fail. Maximum 20 pages per request.
- This tool can read Jupyter notebooks (.ipynb files) and returns all cells
  with their outputs, combining code, text, and visualizations.
- This tool can only read files, not directories. To read a directory, use
  an ls command via the Bash tool.
- You will regularly be asked to read screenshots. If the user provides a
  path to a screenshot, ALWAYS use this tool to view the file at the path.
  This tool will work with all temporary file paths.
- If you read a file that exists but has empty contents you will receive a
  system reminder warning in place of file contents.
```

### Constants

| Constant              | Value                                                                                                                                                          |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MAX_LINES_TO_READ`   | `2000`                                                                                                                                                         |
| `FILE_UNCHANGED_STUB` | `"File unchanged since last read. The content from the earlier Read tool_result in this conversation is still current — refer to that instead of re-reading."` |

---

## Write Tool (`FILE_WRITE_TOOL_NAME = 'Write'`)

### Description

```
Write a file to the local filesystem.
```

### Full Prompt (`getWriteToolDescription`)

```
Writes a file to the local filesystem.

Usage:
- This tool will overwrite the existing file if there is one at the provided path.
- If this is an existing file, you MUST use the Read tool first to read the
  file's contents. This tool will fail if you did not read the file first.
- Prefer the Edit tool for modifying existing files — it only sends the diff.
  Only use this tool to create new files or for complete rewrites.
- NEVER create documentation files (*.md) or README files unless explicitly
  requested by the User.
- Only use emojis if the user explicitly requests it. Avoid writing emojis
  to files unless asked.
```

---

## Edit Tool (`FILE_EDIT_TOOL_NAME = 'Edit'`)

### Description

_(No separate short description — uses the full prompt as description)_

### Full Prompt (`getEditToolDescription`)

```
Performs exact string replacements in files.

Usage:
- You must use your Read tool at least once in the conversation before editing.
  This tool will error if you attempt an edit without reading the file.
- When editing text from Read tool output, ensure you preserve the exact
  indentation (tabs/spaces) as it appears AFTER the line number prefix. The
  line number prefix format is: spaces + line number + arrow (or line number
  + tab in compact mode). Everything after that is the actual file content to
  match. Never include any part of the line number prefix in the old_string or
  new_string.
- ALWAYS prefer editing existing files in the codebase. NEVER write new files
  unless explicitly required.
- Only use emojis if the user explicitly requests it. Avoid adding emojis to
  files unless asked.
- The edit will FAIL if old_string is not unique in the file. Either provide a
  larger string with more surrounding context to make it unique or use
  replace_all to change every instance of old_string.
- Use replace_all for replacing and renaming strings across the file. This
  parameter is useful if you want to rename a variable for instance.
```

### Ant-Only Addition

```
- Use the smallest old_string that's clearly unique — usually 2-4 adjacent
  lines is sufficient. Avoid including 10+ lines of context when less uniquely
  identifies the target.
```

---

## Usage Hierarchy

The system prompt's `getUsingYourToolsSection` enforces this tool hierarchy:

```
Bash → Read > Edit > Write > Glob > Grep
```

- Use `Read` instead of `cat`, `head`, `tail`, `sed`
- Use `Edit` instead of `sed`, `awk`
- Use `Write` only for new files or complete rewrites (prefer `Edit` for modifications)
- Use `Bash` only as a last resort for file operations
