---
description: Create detailed implementation task with steps, dependencies, and parallel execution guidance
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep"]
---

# Create Implementation Task

Generate a detailed, actionable task file for a feature or fix.

**Task to create:** $ARGUMENTS

## Phase 1: Context Gathering

### 1.1 Understand the Requirement
If a PRD/spec exists, read it to understand:
- Exact requirements
- Acceptance criteria
- Edge cases
- Related features

### 1.2 Analyze Current Codebase
```bash
# Find related files
grep -rn "[relevant keywords]" --include="*.ts" --include="*.tsx" --include="*.js" --include="*.py" . 2>/dev/null | head -30

# Check existing patterns
# Look for similar implementations to follow
```

### 1.3 Check Dependencies
- What must be completed first?
- What can be done in parallel?
- What would this block?

### 1.4 Review Standards
Check CLAUDE.md and existing code for:
- Naming conventions
- Code patterns used
- File organization
- Testing approach

## Phase 2: Task Breakdown

Break into atomic sub-tasks that are:
- **Independent** where possible
- **Testable** individually
- **< 2 hours** of work each
- **Clear** about what "done" looks like

## Phase 3: Generate Task File

Create `.claude/tasks/[task-name].md`:

```markdown
# Task: [Task Name]

## Status: ⬜ Not Started

## Meta
- **Priority**: P0/P1/P2
- **Effort**: S (< 1 day) / M (1-3 days) / L (3+ days)
- **Created**: [DATE]
- **Branch**: `feature/[task-name]`

## Goal
[2-3 sentences describing what this task accomplishes and why it matters]

## Requirements
[Extracted from PRD/spec if available]
- [ ] Requirement 1
- [ ] Requirement 2
- [ ] Requirement 3

---

## Dependencies

### Blocked By (must complete first)
| Task | Status | Why Blocking |
|------|--------|--------------|
| [task-name] | ⬜/🔄/✅ | [reason] |

### Blocks (waiting on this)
| Task | Impact |
|------|--------|
| [task-name] | [what can't proceed] |

### Can Run In Parallel
These tasks have no dependencies with this one - safe for concurrent Claude instances:
- [ ] [task-name] - [brief description]
- [ ] [task-name] - [brief description]

---

## Pre-Implementation Checklist

Before starting, verify:

- [ ] Dependencies above are ✅ completed
- [ ] Branch created: `git checkout -b feature/[task-name]`
- [ ] Understand the acceptance criteria
- [ ] Reviewed similar existing implementations
- [ ] Know which files will be modified

### Context to Review
Read these files/sections first:
1. `[file-path]` - [what to understand from it]
2. `[file-path]` - [what to understand from it]
3. `.claude/docs/[relevant-doc].md` - [context]

### Existing Patterns to Follow
```typescript
// Example of the pattern to use (from existing code)
[code example showing the convention to follow]
```

---

## Implementation Steps

### Step 1: [Step Name]
**Estimated**: 30 min

**Goal**: [What this step accomplishes]

**Files to modify**:
- `path/to/file.ts` - [what changes]

**Implementation**:
```typescript
// Specific code or pseudocode
```

**Verify**:
- [ ] [How to verify this step is complete]

---

### Step 2: [Step Name]
**Estimated**: 45 min

**Goal**: [What this step accomplishes]

**Files to modify**:
- `path/to/file.ts` - [what changes]
- `path/to/another.ts` - [what changes]

**Implementation**:
[Detailed instructions]

**Verify**:
- [ ] [Verification step]

---

### Step 3: [Step Name]
[Continue pattern...]

---

## Code Standards

### Variables & Naming
Follow existing project conventions:
```typescript
// Use these patterns:
const [example from codebase]

// NOT these:
const [anti-pattern to avoid]
```

### Error Handling
```typescript
// Standard error handling pattern for this project:
[example from codebase]
```

### Types
```typescript
// Type definitions should follow:
[example from codebase]
```

---

## Files to Modify

| File | Action | Description |
|------|--------|-------------|
| `src/path/file.ts` | Modify | [what changes] |
| `src/path/new-file.ts` | Create | [purpose] |
| `tests/file.test.ts` | Create | [test coverage] |

---

## Testing

### Unit Tests
```typescript
// Tests to write:
describe('[Feature]', () => {
  it('should [expected behavior]', () => {
    // test implementation
  });
});
```

### Manual Testing
1. [ ] [Manual test step 1]
2. [ ] [Manual test step 2]
3. [ ] [Manual test step 3]

### Edge Cases to Test
- [ ] [Edge case 1]
- [ ] [Edge case 2]

---

## Verification Checklist

Before marking complete:

- [ ] All implementation steps done
- [ ] Code follows project patterns (see Code Standards above)
- [ ] No TypeScript errors: `pnpm typecheck`
- [ ] Build succeeds: `pnpm build`
- [ ] Tests pass: `pnpm test`
- [ ] Manual testing completed
- [ ] No console.logs or debug code left
- [ ] Edge cases handled

---

## Completion

When done:
1. Update status in this file: `## Status: ✅ Complete`
2. Run `/task-complete [task-name]` to archive
3. Update `.claude/tasks/README.md` status table
4. Commit: `git commit -m "feat: [task description]"`

---

## References

- PRD/Spec: `[path or link]`
- Related tasks: `[task-names]`
- Design doc: `[path or link]`
- External docs: `[urls]`

---

## Notes

[Any additional context, gotchas, or decisions made during implementation]
```

## Phase 4: Update Task README

Update `.claude/tasks/README.md` to include this task in the appropriate table.

## Phase 5: Check Parallel Safety

### Identify Parallel-Safe Tasks
Tasks are parallel-safe if they:
- Modify different files
- Don't depend on each other's output
- Don't modify shared state

### Flag Conflicts
Tasks that CANNOT run in parallel:
- Same file modifications
- Database schema changes
- Shared configuration changes
- Sequential dependencies

Add warnings to task file if conflicts exist.

## Output

```markdown
# Task Created

**File**: `.claude/tasks/[task-name].md`
**Priority**: [P0/P1/P2]
**Effort**: [S/M/L]
**Steps**: [X] implementation steps

## Summary
[Brief description]

## Dependencies
- Blocked by: [X tasks]
- Can parallel with: [X tasks]

## Quick Start
```bash
git checkout -b feature/[task-name]
# Read pre-implementation checklist first
# Then follow implementation steps
```

Run `/tasks` to see full task board
```
