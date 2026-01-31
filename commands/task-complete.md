---
description: Mark task complete, verify, and archive it
allowed-tools: ["Read", "Write", "Edit", "Bash"]
---

# Complete and Archive Task

Mark a task as complete, run verification, and move to archive.

**Task to complete:** $ARGUMENTS

## Phase 1: Find and Read Task

```bash
# Find the task file
ls .claude/tasks/*$ARGUMENTS*.md 2>/dev/null
```

Read the task file and extract:
- Verification checklist
- Files that were modified
- Tests that should pass

## Phase 2: Verification

### 2.1 Run Project Checks
```bash
# TypeScript check
pnpm typecheck 2>&1 || npm run typecheck 2>&1 || echo "No typecheck script"

# Build check
pnpm build 2>&1 || npm run build 2>&1 || echo "No build script"

# Test check
pnpm test 2>&1 || npm run test 2>&1 || echo "No test script"
```

### 2.2 Verify Task Checklist
Read the "Verification Checklist" from the task file and confirm:
- [ ] All implementation steps marked done
- [ ] Build passes
- [ ] Tests pass
- [ ] No debug code left

### 2.3 Check for Uncommitted Changes
```bash
git status --short
git diff --stat
```

## Phase 3: Update Task Status

Update the task file:
```markdown
## Status: ✅ Complete
## Completed: [DATE]
```

Add completion notes if any issues were discovered or deviations from plan.

## Phase 4: Archive Task

### 4.1 Create Archive Directory
```bash
mkdir -p .claude/tasks/archive
```

### 4.2 Move Task File
```bash
mv .claude/tasks/[task-name].md .claude/tasks/archive/
```

### 4.3 Update Archive Index

If `.claude/tasks/archive/README.md` doesn't exist, create it:

```markdown
# Archived Tasks

Completed tasks for historical reference.

## Recently Completed

| Task | Completed | Duration | Notes |
|------|-----------|----------|-------|
```

Add entry for this task:
```markdown
| [task-name](task-name.md) | [DATE] | [duration if tracked] | [brief notes] |
```

## Phase 5: Update Main README

Update `.claude/tasks/README.md`:
- Remove task from active tables
- Add to "Recently Completed" section if exists
- Update counts

## Phase 6: Update CHANGELOG

Add entry to `.claude/docs/CHANGELOG.md`:
```markdown
## [DATE]

### [Task Category]
- Completed: [task name] - [brief description of what was done]
```

## Phase 7: Unblock Dependent Tasks

Check if any tasks were blocked by this one:
```bash
grep -l "[task-name]" .claude/tasks/*.md 2>/dev/null
```

For each blocked task:
- Check if all blockers are now complete
- Update status from 🚫 Blocked to ⬜ Not Started if unblocked

## Output

```markdown
# Task Completed

**Task**: [task-name]
**Completed**: [DATE]
**Archived**: `.claude/tasks/archive/[task-name].md`

## Verification Results
| Check | Result |
|-------|--------|
| Build | ✅ Pass |
| TypeScript | ✅ Pass |
| Tests | ✅ Pass / ⚠️ X failures |

## Files Modified
- `path/to/file.ts`
- `path/to/other.ts`

## Tasks Unblocked
- [task-name] - now ready to start
- [task-name] - now ready to start

## Updated Files
- `.claude/tasks/archive/[task-name].md` - archived task
- `.claude/tasks/README.md` - updated status
- `.claude/docs/CHANGELOG.md` - added entry

---

**Next recommended task**: [highest priority unblocked task]

Run `/tasks` to see updated task board
```

## Error Handling

### If Verification Fails
```markdown
# Task Completion Blocked

**Task**: [task-name]
**Issue**: Verification failed

## Failures
- [ ] Build failed: [error]
- [ ] Tests failed: [X failures]
- [ ] TypeScript errors: [count]

## Required Actions
1. Fix the issues above
2. Run `/task-complete [task-name]` again

Task NOT archived - still in active tasks.
```

### If Task Not Found
```markdown
# Task Not Found

Could not find task matching: [argument]

Available tasks:
[list of task files]

Usage: `/task-complete [task-name]`
```
