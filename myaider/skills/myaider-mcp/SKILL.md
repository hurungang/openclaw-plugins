---
name: myaider-mcp
version: 0.1.0
description: >
  Connect to the MyAider MCP server and invoke its tools. Use this skill when
  the user wants to interact with MyAider tools directly, call any tool exposed
  by the MyAider MCP, or when another skill requires MyAider MCP capabilities.
metadata:
  openclaw:
    homepage: https://www.myaider.ai/mcp
    emoji: "🤖"
---

# MyAider MCP Client

This skill enables your OpenClaw agent to connect to the [MyAider](https://www.myaider.ai) MCP server and invoke its tools.

## Setup

Before using this skill, make sure the MyAider MCP server is configured in your OpenClaw agent.

1. Visit **https://www.myaider.ai/mcp** and follow the setup instructions for your agent.
2. Once configured, the MCP server will expose tools with identifiers of the form `mcp__{SERVER_NAME}__<tool-name>`.

> **Note on naming:** The server name (`{SERVER_NAME}`) is whatever name the user assigned when configuring the MCP. It may not be `myaider`. Always discover the actual name at runtime.

## Discovering the Server Name

The MyAider MCP exposes a uniquely named tool called `get_myaider_skills`. To discover the server name:

1. Search the available tools for any tool whose name ends with `get_myaider_skills`.
2. The full identifier will be `mcp__{SERVER_NAME}__get_myaider_skills`.
3. Extract `{SERVER_NAME}` from the middle segment.
4. Use `mcp__{SERVER_NAME}__<tool-name>` for all subsequent calls.

If no such tool is found, the MyAider MCP is not yet configured. Direct the user to https://www.myaider.ai/mcp for setup instructions.

## Invoking MyAider Tools

Once the server name is discovered, invoke any MyAider tool using:

```
mcp__{SERVER_NAME}__<tool-name>
```

with the required arguments as documented by that tool.

### Common MyAider MCP Tools

| Tool name | Purpose |
|---|---|
| `get_myaider_skills` | Retrieve all available skills from the MyAider server |
| `get_myaider_skill_updates` | Retrieve skills with their latest `updated_at` timestamps |

Additional tools depend on the skills and integrations configured in the user's MyAider account.

## Example Usage

- "List the tools available in my MyAider MCP"
- "Call the MyAider tool to get my skills"
- "Use MyAider MCP to run [tool-name]"

## Error Handling

- If the MCP server is unreachable, inform the user and suggest verifying their MyAider configuration at https://www.myaider.ai/mcp.
- If a required tool argument is missing, ask the user to provide it before proceeding.
