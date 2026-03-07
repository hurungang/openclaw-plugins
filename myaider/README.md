# openclaw-plugin-myaider

An OpenClaw plugin that integrates with [MyAider](https://www.myaider.ai) via MCP (Model Context Protocol). It provides two skills:

1. **myaider-mcp** — a basic MCP client skill that lets your agent connect to the MyAider MCP server and invoke any of its tools.
2. **myaider-skill-importer** — a skill that downloads dynamic skills from the MyAider MCP server and installs them as local OpenClaw skills (using skill-creator), including upgrade/sync support.

---

## Overview

MyAider is an MCP hub that exposes a catalogue of skills as MCP tools. This plugin bridges MyAider and OpenClaw so that:

- Agents can call MyAider tools directly through the `myaider-mcp` skill.
- Agents can import, create, and keep up-to-date a local copy of every MyAider skill through the `myaider-skill-importer` skill.

---

## Installation

### Via OpenClaw Extensions

```bash
cd ~/.openclaw/extensions/
git clone https://github.com/hurungang/openclaw-plugins
# or, if already cloned, ensure you have the latest:
git pull
```

Then restart OpenClaw:

```bash
openclaw gateway restart
```

### Prerequisites

Before using these skills, configure the MyAider MCP server in your agent:

1. Go to **https://www.myaider.ai/mcp**
2. Follow the setup instructions for your agent type
3. Note the server name you choose — it will be used as `{SERVER_NAME}` in tool identifiers (e.g. `mcp__myaider__get_myaider_skills`)

---

## Skills

### `myaider-mcp`

A basic MCP client skill for invoking MyAider tools directly.

**Trigger examples:**
- "List the tools available in my MyAider MCP"
- "Use MyAider MCP to run [tool-name]"
- "Call the MyAider tool to get my skills"

The skill automatically discovers the MyAider server name by searching for the uniquely-named `get_myaider_skills` tool, so it works regardless of what name you gave the server.

---

### `myaider-skill-importer`

Imports skills from the MyAider MCP server and creates local OpenClaw skill files using `skill-creator`.

**Import workflow:**
1. Discovers the MyAider MCP server name
2. Fetches all available skills via `get_myaider_skills`
3. Presents skills to the user for selection
4. Creates each selected skill as a local file (with full tool schemas embedded for token efficiency)

**Upgrade workflow:**
1. Fetches latest skill definitions with `get_myaider_skill_updates`
2. Compares remote `updated_at` timestamps against local skills (`source: myaider`)
3. Upgrades outdated skills and installs new ones

**Trigger examples:**
- "Import my MyAider skills"
- "Create skills from MyAider"
- "Upgrade my MyAider skills"
- "Sync my MyAider skills to the latest version"

> **Requirement:** The `skill-creator` skill must be installed. If it is not available, the importer will prompt you to install it first.

---

## File Structure

```
myaider/
├── README.md
├── package.json
├── openclaw.plugin.json
└── skills/
    ├── myaider-mcp/
    │   └── SKILL.md          # Basic MCP client skill
    └── myaider-skill-importer/
        └── SKILL.md          # Skill import & upgrade workflow
```

---

## How It Works

### Server Name Discovery

MyAider MCP tool identifiers follow the pattern `mcp__{SERVER_NAME}__<tool-name>`. The server name is set by the user at configuration time and may differ from `myaider`. Both skills discover the actual name at runtime by searching for the uniquely-named `get_myaider_skills` tool — they never hardcode a name.

### Token Efficiency

When importing a skill, `myaider-skill-importer` embeds the full tool descriptions and parameter schemas directly in the generated skill file. This means the agent can use the skill without calling the MCP protocol for tool introspection, reducing token overhead.

### Upgrade Tracking

Every skill created by `myaider-skill-importer` includes `source: myaider` and `updated_at` in its YAML frontmatter. The upgrade workflow uses these fields to detect which skills need refreshing.

---

## License

MIT
