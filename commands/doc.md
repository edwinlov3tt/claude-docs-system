---
description: Update documentation after coding session (manual alternative to /sync)
allowed-tools: ["Read", "Edit", "Write", "Bash"]
---

# Documentation Update

Review recent changes and update project documentation.

## Steps

### 1. Analyze Changes
```bash
git diff HEAD~1 --name-only 2>/dev/null || git status --short
git log -3 --pretty=format:"%h %s" 2>/dev/null
```

### 2. Update CHANGELOG.md
Add entry under today's date with what changed.

### 3. Check for Issues
If bugs or edge cases were encountered, add to KNOWN_ISSUES.md.

### 4. Update Service/Component Docs
If external services or components changed, update relevant docs.

## Output

```markdown
## Documentation Updated

- CHANGELOG.md - [Added entry for X]
- KNOWN_ISSUES.md - [X new issues / No changes]
- Other docs - [Updated / No changes]
```
