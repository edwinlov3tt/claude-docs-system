---
description: View task board status, manage tasks, identify what can run in parallel
allowed-tools: ["Read", "Write", "Edit", "Bash"]
---

# Task Board Status

View all tasks, their status, dependencies, and identify parallel execution opportunities.

## Phase 1: Scan Tasks

### 1.1 Find All Tasks
```bash
# List task files
ls -la .claude/tasks/*.md 2>/dev/null | grep -v README

# Count by status
echo "=== Task Counts ==="
grep -l "Status: ⬜" .claude/tasks/*.md 2>/dev/null | wc -l | xargs echo "Not Started:"
grep -l "Status: 🔄" .claude/tasks/*.md 2>/dev/null | wc -l | xargs echo "In Progress:"
grep -l "Status: ✅" .claude/tasks/*.md 2>/dev/null | wc -l | xargs echo "Complete:"
```

### 1.2 Read Each Task
For each task file, extract:
- Status (⬜/🔄/✅)
- Priority (P0/P1/P2)
- Effort (S/M/L)
- Dependencies (blocked by / blocks)
- Parallel safety

## Phase 2: Build Dependency Graph

### 2.1 Map Dependencies
Create a dependency map:
```
[task-a] → [task-b] → [task-d]
                   ↘
[task-c] ──────────→ [task-e]
```

### 2.2 Identify Blockers
- Tasks with incomplete dependencies = BLOCKED
- Tasks with no dependencies = READY

### 2.3 Identify Parallel Opportunities
Group tasks that can run simultaneously:
- Different file modifications
- No shared dependencies
- Independent outcomes

## Phase 3: Generate Status Report

```markdown
# Task Board

**Generated**: [DATE]
**Total Tasks**: [X]

## Quick Stats

| Status | Count | Tasks |
|--------|-------|-------|
| ⬜ Not Started | X | [list] |
| 🔄 In Progress | X | [list] |
| ✅ Complete | X | [list] |
| 🚫 Blocked | X | [list] |

---

## Ready to Start (No Blockers)

These tasks have all dependencies met and can be started immediately:

| Task | Priority | Effort | Files |
|------|----------|--------|-------|
| [task-name](tasks/task.md) | P0 | M | `src/api/`, `src/lib/` |

---

## Parallel Execution Groups

Tasks within each group can be worked on simultaneously by different Claude instances.

### Group A (Independent)
Safe to run in parallel - no file conflicts:
- [ ] `task-1` - [brief] - modifies: `src/components/`
- [ ] `task-2` - [brief] - modifies: `src/api/`
- [ ] `task-3` - [brief] - modifies: `src/utils/`

### Group B (Independent)  
- [ ] `task-4` - [brief] - modifies: `workers/`
- [ ] `task-5` - [brief] - modifies: `tests/`

### ⚠️ Sequential Only (File Conflicts)
These tasks modify the same files and MUST be done one at a time:
- `task-6` and `task-7` both modify `src/lib/database.ts`
- `task-8` and `task-9` both modify `package.json`

---

## In Progress

| Task | Started | Assignee | Progress |
|------|---------|----------|----------|
| [task-name](tasks/task.md) | [date] | [who] | [X/Y steps] |

---

## Blocked Tasks

| Task | Blocked By | Status of Blocker |
|------|------------|-------------------|
| [task-name] | [blocking-task] | 🔄 In Progress |

---

## Dependency Tree

```
[Visual representation of task dependencies]

P0 Blockers:
  └── task-auth ⬜
        └── task-api 🚫 (waiting)
              └── task-frontend 🚫 (waiting)

Parallel Track:
  ├── task-ui-1 ⬜ (ready)
  ├── task-ui-2 ⬜ (ready)  
  └── task-ui-3 ⬜ (ready)
```

---

## Completed (Recent)

| Task | Completed | Duration |
|------|-----------|----------|
| [task-name] | [date] | [time] |

---

## Archived

See `.claude/tasks/archive/` for completed task history.

---

## Commands

- `/task [name]` - Create new detailed task
- `/task-complete [name]` - Archive completed task
- `/tasks` - Refresh this status board

---

## Multi-Instance Safety Guide

When running multiple Claude Code instances:

### ✅ Safe to Parallelize
- Tasks in different "Parallel Execution Groups"
- Tasks modifying completely different directories
- Independent test writing

### ❌ Never Parallelize  
- Tasks modifying same files
- Database migrations
- Package.json / lock file changes
- Shared configuration files
- Tasks with explicit dependencies

### Best Practice
1. Check this board before starting
2. Claim a task by changing status to 🔄
3. Work only on files listed in that task
4. Complete and archive before starting another in same group
```

## Phase 4: Update README

If `.claude/tasks/README.md` exists, update the status tables. If not, create it:

```markdown
# Project Tasks

Track implementation tasks, dependencies, and parallel execution opportunities.

## Status Legend
- ⬜ Not Started
- 🔄 In Progress  
- ✅ Complete
- 🚫 Blocked

## Current Tasks

[Auto-generated table from task scan]

| Task | Priority | Status | Blocked By |
|------|----------|--------|------------|
| [task](task.md) | P0 | ⬜ | - |

## Parallel Execution

[Auto-generated from analysis]

## Archive

Completed tasks: `archive/`

---

## How to Work on Tasks

1. Run `/tasks` to see current board
2. Pick an unblocked task
3. Change status to 🔄 In Progress
4. Follow implementation steps in task file
5. Run `/task-complete [name]` when done

## Creating New Tasks

```
/task [feature or requirement description]
```
```

## Output

Display the full status board markdown, highlighting:
1. Tasks ready to start
2. Parallel execution opportunities
3. Current blockers
4. Recommended next actions
