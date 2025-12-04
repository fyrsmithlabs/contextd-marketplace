# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Claude Code plugin for [contextd](https://github.com/fyrsmithlabs/contextd) - cross-session memory and context management for AI agents. Requires contextd MCP server running.

## Plugin Structure

```
plugin.json           # Plugin manifest (skills + commands)
skills/               # SKILL.md files for Claude Code skills
  using-contextd/     # Introduction to contextd tools
  cross-session-memory/  # Learning loop: search → work → record
  checkpoint-workflow/   # Save/resume session state
  error-remediation/     # Error diagnosis and pattern tracking
  session-lifecycle/     # Session start/end management
commands/             # Slash command definitions
```

## Development

No build system - content-only plugin (markdown + JSON).

**Validation:**
```bash
# Validate plugin.json structure
jq . plugin.json

# Check all skills referenced exist
jq -r '.skills[]' plugin.json | xargs -I {} ls {}/SKILL.md

# Check all commands referenced exist
jq -r '.commands[]' plugin.json | xargs -I {} ls {}
```

**Local testing:**
```bash
claude plugins install . --local
```

## Skill Development (MANDATORY)

**REQUIRED:** Use `superpowers:writing-skills` for ANY skill creation or modification.

### TDD Cycle for Skills

Skills follow RED-GREEN-REFACTOR:

1. **RED**: Run baseline test with subagent WITHOUT the skill/change - document failures
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

### Checklist Before Committing Skill Changes

- [ ] Ran baseline test (RED phase)
- [ ] Description starts with "Use when..."
- [ ] Word count within target
- [ ] Verified with subagent test (REFACTOR phase)

## Skill Testing

**REQUIRED:** Use `superpowers:testing-skills-with-subagents` for testing skill changes.

### Test Resources

```
tests/
├── README.md                    # Testing methodology
├── pressure-scenarios/          # Discipline skill tests
│   ├── template.md              # Scenario template
│   ├── cross-session-memory/    # 3 scenarios
│   ├── checkpoint-workflow/     # 2 scenarios
│   ├── error-remediation/       # 2 scenarios
│   └── session-lifecycle/       # 2 scenarios
└── evaluations/                 # JSON test cases
    └── *.json                   # Expected behaviors per skill
```

### Running Tests

```bash
# Run pressure test
/contextd:test-skill cross-session-memory 1

# Manual subagent test
# Use Task tool with scenario content from tests/pressure-scenarios/
```

### Test Types

| Skill Type | Test Method |
|------------|-------------|
| Discipline (has compliance rules) | Pressure scenarios with 3+ combined pressures |
| Reference (API docs, guides) | JSON evaluations with expected behaviors |

## MCP Tool Dependencies

All contextd MCP tools use `mcp__contextd__` prefix:
- `memory_search`, `memory_record`, `memory_feedback`
- `checkpoint_save`, `checkpoint_list`, `checkpoint_resume`
- `remediation_search`, `remediation_record`
- `troubleshoot_diagnose`
- `repository_index`

## Key Concepts

**Tenant ID**: Auto-derived from git remote URL.

**Project ID**: Repository identifier for scoping memories.

**Confidence Scores**: 0-1 values adjusting via feedback.

**Resume Levels**: `summary` (minimal), `context` (balanced), `full` (complete state).
