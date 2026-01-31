---
description: Check documentation health and status
allowed-tools: ["Read", "Bash"]
---

# Documentation Status

Check the current state of project documentation.

## Checks

### 1. Structure
Verify `.claude/docs/` exists with required files.

### 2. Freshness
```bash
echo "=== Doc updates ==="
for f in .claude/docs/*.md; do
  echo "$f: $(git log -1 --format='%ar' -- "$f" 2>/dev/null || echo 'not tracked')"
done

echo "=== Last code commit ==="
git log -1 --format="%ar - %s" 2>/dev/null
```

### 3. Issue Count
Read KNOWN_ISSUES.md and count by severity.

### 4. Service Coverage
List services in `.claude/docs/services/` and compare to detected services in code.

## Output

```markdown
# Documentation Status

## Structure
| File | Status |
|------|--------|
| CHANGELOG.md | ✅/❌ |
| KNOWN_ISSUES.md | ✅/❌ |
| DECISIONS.md | ✅/❌ |
| ARCHITECTURE.md | ✅/❌ |

## Active Issues
- CRITICAL: X
- HIGH: X
- MEDIUM: X
- LOW: X

## Freshness
- Last doc update: [time]
- Last code commit: [time]
- Status: ✅ Current / ⚠️ May need /sync

## Recommendations
1. [Actions needed]
```
