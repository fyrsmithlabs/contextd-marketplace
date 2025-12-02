---
name: cross-session-memory
description: Use when starting any task to search for relevant past learnings, and at task completion to record new insights - implements the learning loop with memory_search, memory_record, and memory_feedback
---

# Cross-Session Memory

## Overview

The learning loop: search before starting, record after completing. Your insights persist and improve with feedback.

## When to Use

**Task Start (ALWAYS)**:
- Before implementing any feature
- Before debugging any issue
- Before answering complex questions
- "Have I solved something like this before?"

**Task Completion**:
- After successfully fixing a bug
- After discovering a useful pattern
- After learning something non-obvious
- Both successes AND failures are valuable

## The Learning Loop

```
┌─────────────────────────────────────────┐
│  1. SEARCH at task start                │
│     memory_search(project_id, query)    │
├─────────────────────────────────────────┤
│  2. DO the work                         │
│     (apply relevant memories if found)  │
├─────────────────────────────────────────┤
│  3. RECORD at completion                │
│     memory_record(project_id, title,    │
│                   content, outcome)     │
├─────────────────────────────────────────┤
│  4. FEEDBACK when memories helped       │
│     memory_feedback(memory_id, helpful) │
└─────────────────────────────────────────┘
```

## Tool Reference

### memory_search

```json
{
  "project_id": "contextd",
  "query": "fixing race conditions in goroutines",
  "limit": 5
}
```

Returns memories ranked by relevance with confidence scores.

### memory_record

```json
{
  "project_id": "contextd",
  "title": "Goroutine race condition fix",
  "content": "Use sync.Mutex for shared state, or channels for communication. Run with -race flag to detect.",
  "outcome": "success",
  "tags": ["go", "concurrency", "debugging"]
}
```

Outcome: `success` or `failure` - both are valuable learnings.

### memory_feedback

```json
{
  "memory_id": "mem_abc123",
  "helpful": true
}
```

Adjusts confidence: helpful increases, unhelpful decreases.

## What to Record

**Good memories**:
- Non-obvious solutions
- Patterns that apply broadly
- Mistakes and why they failed
- Workarounds for known issues

**Skip recording**:
- Trivial fixes (typos, syntax)
- One-off configurations
- Project-specific details (put in CLAUDE.md)

## Quick Reference

| When | Action | Tool |
|------|--------|------|
| Starting task | Search for relevant memories | `memory_search` |
| Memory was helpful | Provide positive feedback | `memory_feedback(helpful=true)` |
| Memory was wrong | Provide negative feedback | `memory_feedback(helpful=false)` |
| Task succeeded | Record the learning | `memory_record(outcome=success)` |
| Task failed | Record what didn't work | `memory_record(outcome=failure)` |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Skipping search "to save time" | Search takes seconds, saves hours |
| Only recording successes | Failures prevent repeated mistakes |
| Vague memory content | Be specific: what, why, how |
| Never giving feedback | Feedback improves future rankings |
