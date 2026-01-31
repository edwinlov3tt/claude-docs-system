---
description: Log a specific issue or bug (manual - /sync does this automatically)
allowed-tools: ["Read", "Write", "Edit"]
---

# Log Issue

**Issue:** $ARGUMENTS

## Add to KNOWN_ISSUES.md

Determine severity:
- CRITICAL: System unusable, data loss, security
- HIGH: Major feature broken, no workaround
- MEDIUM: Feature impaired, workaround exists
- LOW: Minor inconvenience

```markdown
### [SEVERITY] Brief Title
- **Location**: `path/to/file.ts`
- **Symptom**: What goes wrong
- **Workaround**: Temp fix or "None"
- **Proper Fix**: What needs to be done
- **Added**: YYYY-MM-DD
```

Confirm when logged.
