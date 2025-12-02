---
name: using-contextd
description: Use when starting any session with contextd MCP server available - introduces cross-session memory, checkpoints, and error remediation tools for persistent AI agent learning
---

# Using contextd

## Overview

contextd provides cross-session memory and context management via MCP. Your learnings persist across sessions, errors get remembered, and context can be checkpointed and resumed.

## Available Tools

| Category | Tools | Purpose |
|----------|-------|---------|
| **Memory** | `memory_search`, `memory_record`, `memory_feedback` | Cross-session learning |
| **Checkpoint** | `checkpoint_save`, `checkpoint_list`, `checkpoint_resume` | Context preservation |
| **Remediation** | `remediation_search`, `remediation_record` | Error pattern tracking |
| **Troubleshoot** | `troubleshoot_diagnose` | AI-powered error diagnosis |
| **Repository** | `repository_index` | Semantic code search |

## When to Use Other Skills

| Situation | Use Skill |
|-----------|-----------|
| Starting any task | `contextd:cross-session-memory` (search first) |
| Context approaching 70% | `contextd:checkpoint-workflow` |
| Encountering errors | `contextd:error-remediation` |

## Key Concepts

**Tenant ID**: Auto-derived from GitHub remote URL. No configuration needed.

**Project ID**: Passed to memory tools, scopes learnings to project.

**Confidence**: Memories have confidence scores (0-1) that adjust via feedback.

## Quick Start

```
1. memory_search - "Have I solved this before?"
2. Do the work
3. memory_record - "What did I learn?"
4. checkpoint_save - If session is long or context is high
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Not searching at task start | Always `memory_search` first |
| Forgetting to record learnings | `memory_record` at task completion |
| Letting context overflow | `checkpoint_save` at 70% |
| Re-solving fixed errors | `remediation_search` when errors occur |
