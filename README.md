# contextd-marketplace

Claude Code plugin for [contextd](https://github.com/fyrsmithlabs/contextd) - cross-session memory and context management for AI agents.

## Installation

```bash
claude plugins install fyrsmithlabs/contextd-marketplace
```

**Requires**: contextd MCP server running. See [contextd setup](https://github.com/fyrsmithlabs/contextd).

## Skills

| Skill | Purpose |
|-------|---------|
| `contextd:using-contextd` | Introduction to contextd tools |
| `contextd:cross-session-memory` | Learning loop with memory tools |
| `contextd:checkpoint-workflow` | Context preservation and recovery |
| `contextd:error-remediation` | Error diagnosis and pattern matching |

## Commands

| Command | Purpose |
|---------|---------|
| `/contextd:checkpoint` | Quick save current session |
| `/contextd:remember` | Record a learning |
| `/contextd:diagnose` | Troubleshoot an error |
| `/contextd:resume` | Resume from checkpoint |
| `/contextd:status` | Show contextd state |
| `/contextd:search` | Search memories and remediations |

## What is contextd?

contextd provides persistent memory for AI agents:

- **Memory**: Learnings persist across sessions with confidence scoring
- **Checkpoints**: Save and resume context when limits approach
- **Remediation**: Error patterns tracked and recalled automatically
- **Agent-agnostic**: Works with any MCP-compatible agent

## License

MIT
