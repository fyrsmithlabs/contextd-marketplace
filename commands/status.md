Show contextd status for current session and project.

Gather and display:

1. **Session Info**
   - Tenant ID (from git remote)
   - Project path
   - Current session identifier

2. **Memories** (call `mcp__contextd__memory_search` with broad query)
   - Count of memories for this project
   - Recent memories recorded this session
   - Top memories by confidence

3. **Checkpoints** (call `mcp__contextd__checkpoint_list`)
   - Available checkpoints for this project
   - Most recent checkpoint summary

4. **Remediations**
   - Recent remediations used or recorded
   - Count by category

Format as a clean status report showing what contextd knows about this project.
