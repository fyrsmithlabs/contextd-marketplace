# contextd-marketplace

[![Plugin](https://img.shields.io/badge/claude--code-plugin-blue)](https://github.com/fyrsmithlabs/contextd-marketplace)
[![Version](https://img.shields.io/badge/version-0.2.0-green)](https://github.com/fyrsmithlabs/contextd-marketplace/releases)

Claude Code plugin for [contextd](https://github.com/fyrsmithlabs/contextd) - cross-session memory and context management for AI agents.

## Quick Start

### 1. Install contextd Server (Required)

Choose one of these methods:

#### Docker (Recommended)

```bash
docker pull ghcr.io/fyrsmithlabs/contextd:dev
```

Add to `~/.claude.json`:

```json
{
  "mcpServers": {
    "contextd": {
      "command": "docker",
      "args": [
        "run", "-i", "--rm",
        "-v", "contextd-data:/data",
        "ghcr.io/fyrsmithlabs/contextd:dev"
      ]
    }
  }
}
```

#### Homebrew (macOS/Linux)

```bash
brew install fyrsmithlabs/tap/contextd
```

Start Qdrant (required):

```bash
docker run -d --name contextd-qdrant \
  -p 6333:6333 -p 6334:6334 \
  -v contextd-qdrant-data:/qdrant/storage \
  --restart always \
  qdrant/qdrant:v1.12.1
```

Add to `~/.claude.json`:

```json
{
  "mcpServers": {
    "contextd": {
      "command": "contextd",
      "args": ["-mcp"],
      "env": {
        "QDRANT_HOST": "localhost",
        "QDRANT_PORT": "6334"
      }
    }
  }
}
```

#### Binary Download

Download from [GitHub Releases](https://github.com/fyrsmithlabs/contextd/releases) and configure as above.

### 2. Install This Plugin

```bash
# Add the marketplace
/plugin marketplace add fyrsmithlabs/contextd-marketplace

# Install the plugin
/plugin install contextd
```

Or install directly:

```bash
claude plugins install fyrsmithlabs/contextd-marketplace
```

### 3. Restart Claude Code

Restart Claude Code to load both the MCP server and plugin.

## Skills

| Skill | When to Use |
|-------|-------------|
| `contextd:using-contextd` | Overview of all contextd tools |
| `contextd:session-lifecycle` | Session start/end protocols |
| `contextd:cross-session-memory` | Learning loop (search → do → record) |
| `contextd:checkpoint-workflow` | Context preservation at 70% |
| `contextd:error-remediation` | Error diagnosis and pattern matching |

## Commands

| Command | Purpose |
|---------|---------|
| `/contextd:checkpoint` | Save current session state |
| `/contextd:remember` | Record a learning or insight |
| `/contextd:diagnose` | Troubleshoot an error with AI diagnosis |
| `/contextd:resume` | List and resume from checkpoints |
| `/contextd:status` | Show contextd state for project |
| `/contextd:search` | Search memories and remediations |
| `/contextd:test-skill` | Run pressure tests against skills |

## MCP Tools

The plugin provides skills and commands that use these contextd MCP tools:

| Tool | Purpose |
|------|---------|
| `memory_search` | Find relevant learnings from past sessions |
| `memory_record` | Save a new learning or strategy |
| `memory_feedback` | Rate whether a memory was helpful |
| `checkpoint_save` | Save current context for later |
| `checkpoint_list` | List available checkpoints |
| `checkpoint_resume` | Resume from a saved checkpoint |
| `remediation_search` | Find fixes for similar errors |
| `remediation_record` | Record a new error fix |
| `troubleshoot_diagnose` | AI-powered error diagnosis |
| `repository_index` | Index a codebase for semantic search |

## Schema Reference

The plugin includes a JSON Schema for all MCP tool inputs/outputs:

```
schemas/contextd-mcp-tools.schema.json
```

**Usage with @ imports** (lazy loaded):
```
@~/.claude/schemas/contextd-mcp-tools.schema.json
```

The schema is symlinked to `~/.claude/schemas/` on install for global access.

## What is contextd?

contextd provides persistent memory for AI agents:

- **Memory**: Learnings persist across sessions with confidence scoring
- **Checkpoints**: Save and resume context when limits approach
- **Remediation**: Error patterns tracked and recalled automatically
- **Local-first**: Runs entirely on your machine, no cloud required
- **Agent-agnostic**: Works with any MCP-compatible agent

## Troubleshooting

### "contextd tools not available"

1. Check server is running: `curl -s http://localhost:9090/health`
2. Verify MCP config in `~/.claude.json`
3. Restart Claude Code

### "Connection refused"

1. Docker: Check container is running: `docker ps | grep contextd`
2. Homebrew: Check Qdrant is running: `docker ps | grep qdrant`

### Plugin not loading

1. Verify installation: `/plugin list`
2. Reinstall: `/plugin uninstall contextd && /plugin install contextd`

## Links

- [contextd Server](https://github.com/fyrsmithlabs/contextd) - The MCP server
- [contextd Releases](https://github.com/fyrsmithlabs/contextd/releases) - Server downloads
- [Docker Image](https://ghcr.io/fyrsmithlabs/contextd) - Container image
- [Homebrew Tap](https://github.com/fyrsmithlabs/homebrew-tap) - macOS/Linux install

## License

MIT
