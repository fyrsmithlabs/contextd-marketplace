---
name: session-lifecycle
description: Use at session start and before session end - manages contextd memory priming, checkpoint resume, and learning extraction via session_start, session_end, and context_threshold tools
---

# Session Lifecycle

## Overview

Manages the full contextd session lifecycle: priming context at start with memories and checkpoints, extracting learnings at end, and auto-checkpointing when context is high.

## When to Use

**Session Start (ALWAYS)**:
- Beginning of every new session
- After resuming from a checkpoint
- When starting work on a project

**Session End (ALWAYS)**:
- Before clearing context with `/clear`
- Before context compaction
- At the end of a work session
- When task is complete or blocked

**Context Threshold**:
- Context usage exceeds 70%
- Long-running session approaching limits
- Before risky operations that might overflow context

## Session Lifecycle Flow

```
┌────────────────────────────────────────┐
│  1. SESSION START                      │
│     session_start(project_id,          │
│                   session_id)          │
│     • Offers checkpoint resume         │
│     • Surfaces relevant memories       │
├────────────────────────────────────────┤
│  2. DO THE WORK                        │
│     (apply memories, track progress)   │
├────────────────────────────────────────┤
│  3. CONTEXT THRESHOLD (if needed)      │
│     context_threshold(project_id,      │
│                       session_id,      │
│                       percent)         │
│     • Creates auto-checkpoint          │
├────────────────────────────────────────┤
│  4. SESSION END                        │
│     session_end(project_id,            │
│                 session_id,            │
│                 task, approach,        │
│                 outcome, tags)         │
│     • Extracts learnings (Distiller)   │
│     • Creates memories for next time   │
└────────────────────────────────────────┘
```

## On Session Start

At the beginning of every session, call the `session_start` MCP tool:

```json
{
  "project_id": "contextd",
  "session_id": "sess_abc123"
}
```

**Project ID**: Derive from git remote origin URL (e.g., `fyrsmithlabs_contextd`)

**Session ID**: Unique identifier for this session (timestamp, UUID, etc.)

### Handle the Response

**If `checkpoint` is returned:**
- Ask user: "Found previous work from [created_at]: '{summary}'. Resume from this checkpoint?"
- If yes: call `checkpoint_resume` with the checkpoint ID at appropriate level
- If no: continue with fresh context

**Review `memories` array:**
- Surface to user: "Relevant context from previous sessions:"
- List each memory title with confidence score
- Keep top 3-5 most relevant memories in mind during work

### Example Response

```json
{
  "checkpoint": {
    "id": "cp_xyz789",
    "summary": "Implementing hook lifecycle - 4/11 tasks complete",
    "created_at": "2025-12-02 10:45"
  },
  "memories": [
    {
      "id": "mem_1",
      "title": "Registry pattern for DI",
      "confidence": 0.85
    },
    {
      "id": "mem_2",
      "title": "TDD workflow for handlers",
      "confidence": 0.78
    }
  ],
  "resumed": false
}
```

## Before Session End

Before `/clear`, context compaction, or ending work, always summarize and record learnings.

### 1. Summarize the Session

Required fields:

| Field | Description | Example |
|-------|-------------|---------|
| `task` | What were you trying to accomplish? (1-2 sentences) | "Implemented hook lifecycle for contextd with MCP tools" |
| `approach` | What strategy/method did you use? (1-2 sentences) | "TDD with Registry pattern, wrote tests first for each handler" |
| `outcome` | Result: `success`, `failure`, or `partial` | "success" |
| `tags` | Keywords for future discovery (3-5 tags) | ["hooks", "lifecycle", "mcp", "tdd"] |
| `notes` | (Optional) Additional context | "HTTP endpoint needed for Claude Code hooks" |

### 2. Call session_end

```json
{
  "project_id": "contextd",
  "session_id": "sess_abc123",
  "task": "Implemented hook lifecycle for contextd",
  "approach": "TDD with Registry pattern, MCP tools + Claude Code hooks",
  "outcome": "success",
  "tags": ["hooks", "lifecycle", "mcp", "tdd"],
  "notes": "Next: write integration tests"
}
```

### What Gets Recorded

The `session_end` tool:
1. Calls the **Distiller** to extract reusable learnings
2. Creates **memories** for future sessions
3. Executes **session end hook** for cleanup

The Distiller analyzes your session and creates memories like:
- Design decisions and why they were made
- Patterns that worked well
- Mistakes and how to avoid them
- Non-obvious solutions

## On Context Threshold

When context usage is high (>70%), trigger auto-checkpoint.

### Option 1: MCP Tool

```json
{
  "project_id": "contextd",
  "session_id": "sess_abc123",
  "percent": 75
}
```

Returns:

```json
{
  "checkpoint_id": "cp_auto123",
  "message": "Auto-checkpoint created at 75% context usage"
}
```

### Option 2: HTTP Endpoint (from Claude Code hook)

```bash
curl -X POST http://localhost:9090/api/v1/threshold \
  -H "Content-Type: application/json" \
  -d '{
    "project_id": "contextd",
    "session_id": "sess_abc123",
    "percent": 75
  }'
```

Use this from `.claude/hooks/precompact.sh` to trigger auto-checkpoint before context compaction.

## Tool Reference

### session_start

**Input:**
```json
{
  "project_id": "contextd",
  "session_id": "sess_abc123"
}
```

**Output:**
```json
{
  "checkpoint": {
    "id": "cp_xyz",
    "summary": "Previous work summary",
    "created_at": "2025-12-02 10:45"
  },
  "memories": [
    {"id": "mem_1", "title": "Memory title", "confidence": 0.85}
  ],
  "resumed": false
}
```

### session_end

**Input:**
```json
{
  "project_id": "contextd",
  "session_id": "sess_abc123",
  "task": "What you accomplished",
  "approach": "How you did it",
  "outcome": "success|failure|partial",
  "tags": ["tag1", "tag2"],
  "notes": "Optional additional context"
}
```

**Output:**
```json
{
  "memories_created": 1,
  "message": "Session ended. Outcome: success. Learnings extracted."
}
```

### context_threshold

**Input:**
```json
{
  "project_id": "contextd",
  "session_id": "sess_abc123",
  "percent": 75
}
```

**Output:**
```json
{
  "checkpoint_id": "cp_auto123",
  "message": "Auto-checkpoint created at 75% context usage"
}
```

## Writing Good Session Summaries

### Task Field

Be specific about the goal:

| Bad | Good |
|-----|------|
| "Fixed bugs" | "Fixed memory_feedback collection bug causing null hypothesis failures" |
| "Added feature" | "Implemented session lifecycle hooks for contextd" |
| "Worked on code" | "Refactored Registry to use interface pattern for DI" |

### Approach Field

Describe the strategy:

| Bad | Good |
|-----|------|
| "Coded it" | "TDD with test-first approach, wrote failing tests before implementation" |
| "Fixed it" | "Root cause analysis using systematic-debugging skill, then validated fix" |
| "Made changes" | "Registry pattern with interface for mocking, single source of truth" |

### Outcome Field

Be honest:

- **success**: Task completed, tests pass, goals met
- **partial**: Made progress but incomplete, some tests failing
- **failure**: Approach didn't work, need different strategy

Both partial and failure outcomes create valuable memories to avoid repeating mistakes.

### Tags Field

Use specific, searchable tags:

| Bad | Good |
|-----|------|
| ["code"] | ["hooks", "lifecycle", "mcp"] |
| ["work", "stuff"] | ["tdd", "registry-pattern", "di"] |
| ["fix"] | ["bug-fix", "null-pointer", "collection-handling"] |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Forgetting `session_end` before `/clear` | Always call before clearing context |
| Vague task descriptions | Be specific: what exactly did you accomplish? |
| Generic tags | Use searchable keywords: "mcp", "hooks", not "code" |
| Wrong outcome value | Be honest: partial/failure helps avoid repeating mistakes |
| Skipping `session_start` | Even quick sessions benefit from memory priming |
| Not offering checkpoint resume | Always check and ask user if checkpoint found |
| Using outcome as "ok" or "done" | Must be: success, failure, or partial |

## Integration with Other Skills

**Session Start → Cross-Session Memory**:
- `session_start` automatically primes with relevant memories
- Still use `memory_search` for specific queries during work
- Provide `memory_feedback` when memories help (or don't)

**Session End → Cross-Session Memory**:
- `session_end` uses Distiller to create memories
- Distiller extracts reusable patterns from your session summary
- Memories appear in future `session_start` calls

**Context Threshold → Checkpoint Workflow**:
- `context_threshold` creates auto-checkpoint
- Use `checkpoint_list` to find it later
- Resume with `checkpoint_resume` at appropriate level

## Quick Reference

| When | Action | Tool |
|------|--------|------|
| Session begins | Check for checkpoint, prime memories | `session_start` |
| Context > 70% | Create auto-checkpoint | `context_threshold` |
| Before `/clear` | Record learnings | `session_end` |
| End of work day | Record learnings | `session_end` |
| Task completed | Record learnings | `session_end` |
| Task blocked | Record learnings (outcome: partial) | `session_end` |

## Example Full Session

```
# 1. START
session_start({
  "project_id": "contextd",
  "session_id": "sess_20251202_1045"
})

# Response: checkpoint offered, 3 memories shown
# User declines checkpoint, reviews memories

# 2. WORK
# ... do the implementation work ...
# ... call memory_search for specific queries ...

# 3. THRESHOLD (if needed)
context_threshold({
  "project_id": "contextd",
  "session_id": "sess_20251202_1045",
  "percent": 75
})

# 4. END
session_end({
  "project_id": "contextd",
  "session_id": "sess_20251202_1045",
  "task": "Implemented session lifecycle hooks for contextd",
  "approach": "TDD with Registry pattern, wrote handlers for session_start, session_end, context_threshold",
  "outcome": "success",
  "tags": ["hooks", "lifecycle", "mcp", "tdd", "registry-pattern"]
})
```

## CRITICAL: Always Record Learnings

**Before clearing context or ending work:**

1. Summarize: task, approach, outcome, tags
2. Call `session_end`
3. Wait for confirmation

**You will waste time re-discovering things if you skip `session_end`.**

The next session depends on the learnings you record in this one. Make it count.
