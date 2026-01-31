---
description: Initialize documentation structure for a new project
allowed-tools: ["Read", "Write", "Bash"]
---

# Initialize Documentation

Create the `.claude/docs/` folder structure.

## Create Structure

```bash
mkdir -p .claude/docs/services
mkdir -p .claude/docs/components
mkdir -p .claude/tasks/archive
```

## Create Core Files

1. **CHANGELOG.md** - Track changes
2. **KNOWN_ISSUES.md** - Track bugs/issues
3. **DECISIONS.md** - Record architectural decisions
4. **ARCHITECTURE.md** - System overview

Use templates from docs-templates/ or create minimal versions.

## Output

```markdown
## Documentation Initialized

Created:
- .claude/docs/ARCHITECTURE.md
- .claude/docs/CHANGELOG.md
- .claude/docs/DECISIONS.md
- .claude/docs/KNOWN_ISSUES.md
- .claude/docs/services/
- .claude/docs/components/

Next: Run `/audit` to populate with project analysis
```
