# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## What This Is

Claude Code plugin for [contextd](https://github.com/fyrsmithlabs/contextd) - cross-session memory and context management for AI agents. Requires contextd MCP server running.

## Source Documentation

**Official Claude Code Docs:**
- [Plugins Overview](https://code.claude.com/docs/en/plugins) - Plugin structure and components
- [Plugin Marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) - Marketplace schema and publishing

**contextd:**
- [contextd Server](https://github.com/fyrsmithlabs/contextd) - MCP server source
- [contextd Releases](https://github.com/fyrsmithlabs/contextd/releases) - Binary downloads

## Repository Structure

```
.claude-plugin/
├── plugin.json           # Plugin manifest (REQUIRED)
└── marketplace.json      # Marketplace definition
skills/                   # SKILL.md files for Claude Code skills
  using-contextd/         # Introduction to contextd tools
  cross-session-memory/   # Learning loop: search → work → record
  checkpoint-workflow/    # Save/resume session state
  error-remediation/      # Error diagnosis and pattern tracking
  session-lifecycle/      # Session start/end management
commands/                 # Slash command definitions
agents/                   # Custom agent definitions
includes/                 # Reusable protocol snippets
schemas/                  # JSON schemas for MCP tools
tests/                    # Pressure scenarios and evaluations
```

## Development

No build system - content-only plugin (markdown + JSON).

**Validation:**
```bash
# Validate JSON files
jq . .claude-plugin/plugin.json
jq . .claude-plugin/marketplace.json

# Check skills exist
ls skills/*/SKILL.md

# Check commands exist
ls commands/*.md
```

**Local testing:**
```bash
/plugin marketplace add ./
/plugin install contextd
```

## Skill Development

**REQUIRED:** Use `superpowers:writing-skills` for ANY skill creation or modification.

### TDD Cycle for Skills

1. **RED**: Run baseline test with subagent WITHOUT the skill - document failures
2. **GREEN**: Write/edit skill addressing those specific failures
3. **REFACTOR**: Test with subagent, find new gaps, iterate until bulletproof

### Token Efficiency Targets

| Skill Type | Target |
|------------|--------|
| Frequently-loaded | <200 words |
| Standard skills | <500 words |

### Skill Format

```markdown
---
name: skill-name-with-hyphens
description: Use when [triggers/symptoms] - [what it does, third person]
---

# Skill Name

## Overview
Core principle in 1-2 sentences.

## When to Use
Bullet list with symptoms and use cases.

## Tool Reference
Tables with parameters.

## Quick Reference
Scanning table for common operations.

## Common Mistakes
What goes wrong + fixes.
```

## Skill Testing

**REQUIRED:** Use `superpowers:testing-skills-with-subagents` for testing skill changes.

```bash
# Run pressure test
/contextd:test-skill cross-session-memory 1
```

### Test Resources

```
tests/
├── pressure-scenarios/   # Discipline skill tests
│   └── <skill-name>/     # Scenarios per skill
└── evaluations/          # JSON expected behaviors
```

## MCP Tool Dependencies

All contextd MCP tools use `mcp__contextd__` prefix:

| Category | Tools |
|----------|-------|
| Memory | `memory_search`, `memory_record`, `memory_feedback` |
| Checkpoint | `checkpoint_save`, `checkpoint_list`, `checkpoint_resume` |
| Remediation | `remediation_search`, `remediation_record` |
| Troubleshoot | `troubleshoot_diagnose` |
| Repository | `repository_index`, `repository_search` |

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Tenant ID** | Auto-derived from git remote URL |
| **Project ID** | Repository identifier for scoping memories |
| **Confidence Scores** | 0-1 values adjusting via `memory_feedback` |
| **Resume Levels** | `summary` (minimal), `context` (balanced), `full` (complete) |

## Plugin Schema Reference

**plugin.json required fields:**
- `name` - Plugin identifier (kebab-case)
- `description` - Human-readable description
- `version` - Semantic version
- `author` - Object with `name` and optional `email`

**marketplace.json required fields:**
- `name` - Marketplace identifier
- `owner` - Object with `name` and `email`
- `plugins` - Array of plugin entries with `name` and `source`
