List and resume from a previous checkpoint.

1. Call `mcp__contextd__checkpoint_list` with:
   - tenant_id: From git remote or default
   - project_path: Current working directory
   - limit: 10

2. Present checkpoints in a numbered list showing:
   - Name and description
   - When created
   - Summary preview
   - Token count

3. Ask user which checkpoint to resume (or "none" to cancel)

4. Call `mcp__contextd__checkpoint_resume` with:
   - checkpoint_id: Selected checkpoint
   - tenant_id: From git remote or default
   - level: Ask user preference (summary/context/full)

5. Display restored context and summarize where we left off.
