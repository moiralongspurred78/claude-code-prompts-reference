# MCP & Deferred Tool Loading

Tools related to MCP servers/resources and deferred schema loading.

---

## MCPTool

**Source:** `src/tools/MCPTool/prompt.ts`

### Prompt / Description

```
PROMPT = ''
DESCRIPTION = ''
```

> Actual MCP prompt/description are overridden dynamically in `mcpClient.ts`.

---

## ListMcpResourcesTool

**Source:** `src/tools/ListMcpResourcesTool/prompt.ts`

### Description

```
Lists available resources from configured MCP servers.
Each resource object includes a 'server' field indicating which server it's from.

Usage examples:
- listMcpResources
- listMcpResources({ server: "myserver" })
```

### Prompt

```
List available resources from configured MCP servers.
Each returned resource includes standard MCP fields plus a 'server' field.

Parameters:
- server (optional): specific MCP server name; if omitted, all servers.
```

---

## ReadMcpResourceTool

**Source:** `src/tools/ReadMcpResourceTool/prompt.ts`

### Description

```
Reads a specific resource from an MCP server.
- server: MCP server name
- uri: resource URI

Usage:
readMcpResource({ server: "myserver", uri: "my-resource-uri" })
```

### Prompt

```
Reads a specific resource from an MCP server, identified by server name and resource URI.

Parameters:
- server (required)
- uri (required)
```

---

## ToolSearchTool

**Source:** `src/tools/ToolSearchTool/prompt.ts`

### Prompt Head/Tail

```
PROMPT_HEAD:
"Fetches full schema definitions for deferred tools so they can be called."

PROMPT_TAIL:
"Until fetched, only the name is known — there is no parameter schema, so the tool
cannot be invoked. This tool takes a query, matches it against the deferred tool list,
and returns matched tools' complete JSONSchema definitions inside a <functions> block..."
```

### Query Forms

```
- select:Read,Edit,Grep      (exact tool names)
- notebook jupyter           (keyword search, top max_results)
- +slack send                (require 'slack' in name; rank by remaining terms)
```

### Tool Location Hint Behavior

The prompt dynamically inserts one of:

- `Deferred tools appear by name in <system-reminder> messages.` (delta enabled)
- `Deferred tools appear by name in <available-deferred-tools> messages.` (legacy)

### Deferral Logic (`isDeferredTool`)

Tool is deferred if:

- `tool.shouldDefer === true`, OR
- `tool.isMcp === true`

Except never deferred if:

- `tool.alwaysLoad === true`
- tool name is `ToolSearch` itself
- fork-first experiment enables immediate `Agent` availability
- `Brief`/`SendUserFile` need turn-1 availability under KAIROS gates

### Formatting

`formatDeferredToolLine` returns only tool name. Search hints are intentionally not rendered.
